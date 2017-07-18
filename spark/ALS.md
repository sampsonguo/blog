
##### ALS spark代码
* Alternating Least Squares matrix factorization
`
X * Yt = R
`

* Explicit
* Implicit
* NNLS


 Solve a least squares problem, possibly with nonnegativity constraints, by a modified
 projected gradient method.  That is, find x minimising $$||Ax - b||_2$$ given $$A^T A$$ and $$A^T b$$.

 We solve the problem

 <blockquote>
 $$
 min_x 1/2 x^T ata x^T - x^T atb
 $$
 </blockquote>
 where x is nonnegative.

 The method used is similar to one described by Polyak (B. T. Polyak, The conjugate gradient
 method in extremal problems, Zh. Vychisl. Mat. Mat. Fiz. 9(4)(1969), pp. 94-112) for bound-
 constrained nonlinear programming.  Polyak unconditionally uses a conjugate gradient
 direction, however, while this method only uses a conjugate gradient direction if the last
 iteration did not cause a previously-inactive constraint to become active.


##### Ref
1. ALS原始论文：[Yehuda Koren, Robert Bell and Chris Volinsky. Matrix Factorization Techniques for Recommender Systems](papers/Matrix Factorization Techniques for Recommender Systems.pdf)
2. ALS隐式反馈：[Yifan Hu，Yehuda Koren∗，Chris Volinsky. Collaborative Filtering for Implicit Feedback Datasets](papers/Collaborative Filtering for Implicit Feedback Datasets.pdf)
3. ALS-WR：[Yunhong Zhou, Dennis Wilkinson, Robert Schreiber and Rong Pan. Large-scale Parallel Collaborative Filtering for the Netflix Prize](papers/Large-scale Parallel Collaborative Filtering the Netflix Prize.pdf)
