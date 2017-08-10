
#### Spark中的GBDT

##### Spark中tree ensemble的两种方法
* Random Forest
    * bagging
    * 并行
    * Train Fast, Predict Slow, Many Trees, deeper trees
    * 不易过拟合, 树越多越不易
    * 调参容易
* GBDT
    * boosting
    * 串行（树内并行，树间串行）
    * Train Slow, Predict Fast, A Few Trees, shallower trees
    * 容易过拟合， 树越多越容易
    * 不易调参

##### 两种API
* ML库：DataFrame Based, 支持pipeline
* MLlib库：RDD Based，更加底层

##### 支持两种Feature
* category: 枚举特征，离散特征，偏向于Sparse Vector
* continuous: 连续特征, 偏向于Dense Vector

##### MLlib库
* GBTs do not yet support multiclass classification


```
// spark文档中的代码，intellij IDEA 会报错，可改为下方代码
// Train a GradientBoostedTrees model.
// The defaultParams for Regression use SquaredError by default.
val boostingStrategy = BoostingStrategy.defaultParams("Regression") // or Classification
boostingStrategy.setNumIterations(30) // 树个数
boostingStrategy.treeStrategy.setNumClasses = 2 // 类个数，只能设置2
boostingStrategy.treeStrategy.setMaxDepth(5) // 树深度
boostingStrategy.treeStrategy.setMaxBins(32) // 连续值分段分箱数
boostingStrategy.treeStrategy.setMinInstancesPerNode(100) // 树叶子节点最小样本个数
// Empty categoricalFeaturesInfo indicates all features are continuous.
// For example, Map(0 -> 2, 4 -> 10) specifies that feature 0 is binary (taking values 0 or 1) and that feature 4 has 10 categories (values {0, 1, ..., 9}). Note that feature indices are 0-based: features 0 and 4 are the 1st and 5th elements of an instance’s feature vector.
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]()
```

##### ML库

```
// Train a GBT model.
val gbt = new GBTClassifier()
  .setLabelCol("indexedLabel")
  .setFeaturesCol("indexedFeatures")
  .setMaxIter(10)
  .setMaxBins(32)
  .setMaxDepth(5)
  .setMinInstancesPerNode(30)
```

##### Regressor or Classifier ?
ML和MLlib库里
* Regression
    * Squared Error or Absolute Error
* Classification
    * Log Loss

所以，
用regressor做ctr预估，损失函数是不合适的；
用classification做预估，没有办法predict proba。

MLlib因此需要添加代码
```
// Get class probability
val treePredictions = testData.map { point => model.trees.map(_.predict(point.features)) }
val treePredictionsVector = treePredictions.map(array => Vectors.dense(array))
val treePredictionsMatrix = new RowMatrix(treePredictionsVector)
val learningRate = model.treeWeights
val learningRateMatrix = Matrices.dense(learningRate.size, 1, learningRate)
val weightedTreePredictions = treePredictionsMatrix.multiply(learningRateMatrix)
val classProb = weightedTreePredictions.rows.flatMap(_.toArray).map(x => 1 / (1 + Math.exp(-1 * x)))
val classLabel = classProb.map(x => if (x > 0.5) 1.0 else 0.0)
classLabel.collect
```

ML库可能要改源码，要更改predict函数（未实践，留坑）
```

```

##### Feature Importance
* ML库的GBT的支持FeatureImportance

```
// Chain indexers and GBT in a Pipeline.
val pipeline = new Pipeline()
  .setStages(Array(labelIndexer, featureIndexer, gbt, labelConverter))

// Train model. This also runs the indexers.
val model = pipeline.fit(trainingData)

val importances = model
  .stages(2)
  .asInstanceOf[GBTClassificationModel]
  .featureImportances
```

解释可以参见源码注释：

```
/**
   * Estimate of the importance of each feature.
   *
   * Each feature's importance is the average of its importance across all trees in the ensemble
   * The importance vector is normalized to sum to 1. This method is suggested by Hastie et al.
   * (Hastie, Tibshirani, Friedman. "The Elements of Statistical Learning, 2nd Edition." 2001.)
   * and follows the implementation from scikit-learn.

   * See `DecisionTreeClassificationModel.featureImportances`
   */
```

* RF的ML库版本也支持Feature Importance的输出，Demo如下：

```
// Train a RandomForest model.
val rf = new RandomForestClassifier()
  .setLabelCol("indexedLabel")
  .setFeaturesCol("indexedFeatures")
  .setNumTrees(10)

val importances = rf
  .asInstanceOf[RandomForestClassificationModel]
  .featureImportances
```

原理如下：

```
Given a tree ensemble model, RandomForest.featureImportances computes the importance of each feature.
This generalizes the idea of "Gini" importance to other losses, following the explanation of Gini importance from "Random Forests" documentation by Leo Breiman and Adele Cutler, and following the implementation from scikit-learn.
For collections of trees, which includes boosting and bagging, Hastie et al. suggests to use the average of single tree importances across all trees in the ensemble.
And this feature importance is calculated as followed :
Average over trees:
importance(feature j) = sum (over nodes which split on feature j) of the gain, where gain is scaled by the number of instances passing through node
Normalize importances for tree to sum to 1.
Normalize feature importance vector to sum to 1.
References: Hastie, Tibshirani, Friedman. "The Elements of Statistical Learning, 2nd Edition." 2001. - 15.3.2 Variable Importance page 593.
Let's go back to your importance vector :
val importanceVector = Vectors.sparse(12,Array(0,1,2,3,4,5,6,7,8,9,10,11), Array(0.1956128039688559,0.06863606797951556,0.11302128590305296,0.091986700351889,0.03430651625283274,0.05975817050022879,0.06929766152519388,0.052654922125615934,0.06437052114945474,0.1601713590349946,0.0324327322375338,0.057751258970832206))
First, let's sort this features by importance :
importanceVector.toArray.zipWithIndex
            .map(_.swap)
            .sortBy(-_._2)
            .foreach(x => println(x._1 + " -> " + x._2))
// 0 -> 0.1956128039688559
// 9 -> 0.1601713590349946
// 2 -> 0.11302128590305296
// 3 -> 0.091986700351889
// 6 -> 0.06929766152519388
// 1 -> 0.06863606797951556
// 8 -> 0.06437052114945474
// 5 -> 0.05975817050022879
// 11 -> 0.057751258970832206
// 7 -> 0.052654922125615934
// 4 -> 0.03430651625283274
// 10 -> 0.0324327322375338
So what does this mean ?
It means that your first feature (index 0) is the most important feature with a weight of ~ 0.19 and your 11th (index 10) feature is the least important in your model.
```

##### Parameters
* 树个数 （30左右）
* 树深度 （5-10左右）
* maxBins划段 （32左右）
* 最小叶子节点个数 （可根据样本量大小设置，建议100以上）


##### GBDT做Feature Engineer的好处
* 自动筛选
* 自动分段
* 自动交叉

如果GBDT+LR，也可以打印GBDT路径里作为LR特征的权重值，即Feature Importance

##### Spark-GBDT VS Xgboost
* Xgboost 带正则
* Xgboost 资源更少，速度更快
