
##### OVERVIEW
* SPARK SQL
* MLlib & ML
* Graph X
* Spark Streaming

##### Programming
* scala
* java
* python

##### Launch
* Standalone
* Yarn

##### Usage
* Interactive Analysis
* Applications

##### Version
* 2.0 before: RDD
* 2.0 after: Datesets

##### Basics
* Count
* Map, FlatMap, Reduce
* Cache, Collect

##### Apps
* Create project
    * Link Maven Dependence
    * Initialize Spark
    * Shell: using --jars to add jars
    * Parallelized: sc.parallelize(data)
    * External DS:
        * HDFS
        * Text File
        * Sequence File
    * Persist: cache = persist(StorageLevel.MEMORY_ONLY)
    * Func
        * anonymous functions(lambda in python): var inc = (x:Int) => x+1
        * static methods
    * Print
        * collect and print: rdd.collect().foreach(println)
        * print some: take
    * key-value pairs: tuple
    * transformations:
        * map: map, flatmap, mapPartitions, mapPartitionsWithIndex
        * reduce: reduceByKey(better on big dataset), groupByKey, aggregateByKey
        * sort: sortByKey
        * filter
        * sample
        * union
        * intersection
        * distinct
    * Actions
        * reduce: reduce
        * print: take, collect, first, takeSample, takeOrdered
        * save: saveAsTextFile, saveAsSequenceFile, saveAsObjectFile,
    * shuffle
        * shuffle = re-distributing data
        * mapPartitions: sort each partition
        * repartitionAndSortWithinPartitions: sort partitions while simultaneously repartitioning
        * sortBy: global sort
    * Cache
        * least-recently-used (LRU)
        * RDD.unpersist() method
    * Shared Variables
        * Broadcast Variables
        * Accumulators
* Maven package
* Submit to Cluster

##### SQL GUIDE
* Differ from RDD: structured data processing
* SQL, DataFrame, Dataset
* Datasets:


##### RDD VS DF VS DS
* RDD
    * Distributed
    * Immutable
    * Fault tolerant
    * Lazy evaluations
    * Functional transformations
    * Data processing formats
    * Limitations
        * No inbuilt optimization engine
            * catalyst optimizer
            * Tungsten execution engine
        * Handling structured data
* Dataframes
    * Hive Compatibility
    * catalyst optimizer, Tungsten
    * Limitations
        * Compile-time type safety
        * Cannot operate on domain Object
```
case class Person(name : String , age : Int)
val dataframe = sqlContect.read.json("people.json")
dataframe.filter("salary > 10000").show
=> throws Exception : cannot resolve 'salary' given input age , name
```

```
case class Person(name : String , age : Int)
val personRDD = sc.makeRDD(Seq(Person("A",10),Person("B",20)))
val personDF = sqlContect.createDataframe(personRDD)
personDF.rdd // returns RDD[Row] , does not returns RDD[Person]
```

* Datasets
    * Encoders
    * Type Safety
    * Limitations: Requires type casting to String

* Demo
    * Create SparkSession
    * Creating DataFrames or Datasets
    * SQL Queries Programmatically
* Reflection
    * case class -> column name
* Schema
    * create RDD
    * create schema
    * Apply the schema to the RDD
* UDAF
* Save: standard Java serialization
    * saveAsObjectFile: sc.parallelize(Seq(model), 1).saveAsObjectFile()
    * Java Standard Serialization:

```
    val config = new Configuration()
    val fsrm = FileSystem.get(URI.create(path), config)
    fsrm.deleteOnExit(new Path(path))
    fsrm.close()

    val fs = FileSystem.get(URI.create(path), config)
    val os = fs.create(new Path(path))
    val oos = new ObjectOutputStream(os)
    oos.writeObject(model)
    oos.close()
    os.close()
    fs.close()
```

* Performance Tuning
    * Cache

##### MLlib
* ML Algorithms
    * classification
    * regression
    * clustering
    * collaborative filtering
* Featurization
    * feature extraction
    * transformation
    * dimensionality reduction
    * selection
* Piplines
    * constructinng
    * evaluating
    * tuning
* Persistence
    * save
    * load
    * models or piplines
* Utilities
    * linear algebra
    * statistics
    * data handling

* spark.mllib -- maintenance
* spark.ml -- DataFrame based API

* Why ML?
    * User-friendly
    * Uniform
    * Piplines
* Linear Algebra
    * Breeze for jvm
    * Numpy for python

-----
##### Statics
* Correlation
    * Pearson
    * Spearman
* Hypothesis testing
    * Pearson's Chi-squared test

##### Pipelines
* hight-level built on DataFrames
* DataFrame
* Transformer: an DataFrame to another
* Estimator: an algorithm
* Pipeline: multiple Transformer & Estimator
* Parameter

##### Dataframe
* vectors
* text
* images
* structured data
* RDD to DF
    * implicitly:
    * explicitly: Programming
* Columns in a DataFrame are named

##### Transformer
* feature transformers
    * take a DataFrame
    * read a column
    * map it into a new column, feature vectors
    * a new DataFrame with the mapped column appended
* learned models

##### Estimators
* accepts a DataFrame
* produces a Model

##### example
* LogisticRegression -- Estimator
* LogisticRegressionModel -- Transformer
* pipeline -- Estimator
* pipeline.fit -- Transformer

##### Pipeline
* text into words: Tokenizer/Transformers
* words into a numerical feature vector: HashingTF/Transformers
* Learn a prediction model: Estimator/LogisticRegression
* DAG
* Runtime checking
* Unique Pipeline stages

##### ParamMap
* Set parameters for an instance
    * lr.setMaxIter(10)
    * lr.fit()
* Pass a ParamMap to fit()

---
##### features
* Extraction: Extracting features from “raw” data
* Transformation: Scaling, converting, or modifying features
* Selection: Selecting a subset from a larger set of features
* Locality Sensitive Hashing (LSH)

##### Feature Extractors
* TF-IDF
    * HashingTF
    * CountVectorizer
* Word2Vec
* CountVectorizer

##### Feature Transformers
*


##### Extracting
*

##### features
* continuous features
* categorical features


##### Feature Extractors
* TF-IDF
* Word2Vec
* CountVectorizer

##### Feature Transformers
* Tokenizer
* StopWordsRemover
* nn-gram
* Binarizer
* PCA
* PolynomialExpansion
* Discrete Cosine Transform (DCT)
* StringIndexer
* IndexToString
* OneHotEncoder
* VectorIndexer
* Interaction
* Normalizer
* StandardScaler
* MinMaxScaler
* MaxAbsScaler
* Bucketizer
* ElementwiseProduct
* SQLTransformer
* VectorAssembler
* QuantileDiscretizer
* Imputer

##### Feature Selectors
* VectorSlicer
* RFormula
* ChiSqSelector
* Locality Sensitive Hashing
* LSH Operations
* Feature Transformation
* Approximate Similarity Join
* Approximate Nearest Neighbor Search
* LSH Algorithms
* Bucketed Random Projection for Euclidean Distance
* MinHash for Jaccard DistanceTF-IDF
* Word2Vec
* CountVectorizer
* Feature Transformers
* Tokenizer
* StopWordsRemover
* nn-gram
* Binarizer
* PCA
* PolynomialExpansion
* Discrete Cosine Transform (DCT)
* StringIndexer
* IndexToString
* OneHotEncoder
* VectorIndexer
* Interaction
* Normalizer
* StandardScaler
* MinMaxScaler
* MaxAbsScaler
* Bucketizer
* ElementwiseProduct
* SQLTransformer
* VectorAssembler
* QuantileDiscretizer
* Imputer

##### Feature Selectors
* VectorSlicer
* RFormula
* ChiSqSelector
* Locality Sensitive Hashing
* LSH Operations
* Feature Transformation
* Approximate Similarity Join
* Approximate Nearest Neighbor Search
* LSH Algorithms
* Bucketed Random Projection for Euclidean Distance
* MinHash for Jaccard Distance

##### Classification
* Logistic regression
* Binomial logistic regression
* Multinomial logistic regression
* Decision tree classifier
* Random forest classifier
* Gradient-boosted tree classifier
* Multilayer perceptron classifier
* Linear Support Vector Machine
* One-vs-Rest classifier (a.k.a. One-vs-All)
* Naive Bayes

##### Regression
* Linear regression
* Generalized linear regression
* Available families
* Decision tree regression
* Random forest regression
* Gradient-boosted tree regression
* Survival regression
* Isotonic regression

##### Linear methods

##### Decision trees
* Inputs and Outputs
* Input Columns
* Output Columns

##### Tree Ensembles
* Random Forests
    * Inputs and Outputs
    * Input Columns
    * Output Columns (Predictions)
* Gradient-Boosted Trees (GBTs)
    * Inputs and Outputs
    * Input Columns
    * Output Columns (Predictions)

##### cluster
* K-means
    * Input Columns
    * Output Columns
* Latent Dirichlet allocation (LDA)
* Bisecting k-means
* Gaussian Mixture Model (GMM)
        * Input Columns
        * Output Columns

##### Collaborative Filtering
* Explicit
* Implicit feedback

##### Frequent Mining
##### ML Tuning: model selection and hyperparameter tuning
* Limited-memory BFGS (L-BFGS)
* Normal equation solver for weighted least squares
* Iteratively reweighted least squares (IRLS)

##### repartition
##### data types
* Local Vector
* Labeled point
* Local matrix
* Distribute Matrix
    * Row Matrix:A RowMatrix can be created from an RDD[Vector] instance.
    * IndexedRowMatrix: meaningful row indices
    * CoordinateMatrix: a distributed matrix backed by an RDD of its entries
    * BlockMatrix: a distributed matrix backed by an RDD of MatrixBlocks


