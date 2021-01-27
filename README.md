# Recommendation systems

The present repository contains the python3 implementation of 2 different recommendation systems where sparse records of movie ratings are leveraged to produce predictions for the level of satisfaction a user is expected to report in relation to a movie he/she has not rated before. The algorithms are built upon the concept of collaborative filtering such that the prediction of a missing rating for a user is produced through a model trained on a large dataset of historical rating records.
The setting we adopted for the implementation was inspired by the challenge launched by Netflix in 2006 which prompted data scientists worldwide to work on recommendation system solutions which could beat the baseline accuracy of the CineMatch algorithm used at the time. In this context, rating data was collected in a large sparse matrix filled with missing values in the entries associated to user-movie couples for which no rating was recorded, and a value from 1 to 5 corresponding to the evaluation a costumer assigned to a watched movie.

## The dataset

For the implementation, we downloaded the MovieLens 1M dataset downloaded at https://grouplens.org/datasets/movielens/. From the folder of interest, only the *rating.dat* file was exploited. It contains rating entries organized in 3 columns as <userID, movieID, rating>. In total, 1,000,209 ratings were recorded out of 6040 users and 3952 movies.

## The algorithms

The two algorithms we implemented hinge on matrix factorization. Given a sparse matrix X with u users on the rows and m movies on the columns and an arbitrary constant k such that k << m,u, the goal of the two implementations is to factorize X to two smaller matrices U and M of size u x k and k x m such that the u x m matrix P resulting from the dot product of U and M well approximates the non-missing entries of X. In other words, we seek two lower rank matrices which represent each user and movie by vectors of k elements still retaining as much information contained in X as possible. 
Even though the goal is the same, the proposed algorithms work in divergent ways: the first is a simple line-search whereas the second builds upon a gradient descent optimization.

Sources:
- UV Matrix Decomposition: Chapter 9, MMDS book, http://www.mmds.org/
- Gradient Descent Factorization: https://www.cs.uic.edu/~liub/KDD-cup-2007/proceedings/gravity-Tikk.pdf

### The UV Matrix Decomposition
To implement the UV decomposition algorithm, we initialize random U and M matrices in such a way that the starting point of the product between U
and M is already satisfactory but still conserves some degree of stochasticity. To this end, we design the matrices U and M such that that their product yields an u x m matrix with entries corresponding to the average of non-blank ratings of the original matrix. Therefore, all elements of U and M will initially be equal to <img src="https://render.githubusercontent.com/render/math?math=\sqrt{\frac{a}{k}}"> where *a* represents the average non blank rating of X. Then, we perturb these entries with random values drawn from a normal distribution with mean 0 and standard deviation 1. Concerning the algorithm, the minimization method is based on the stepwise adjustment of the single elements of U or M by their substitution with a variable to optimize.
The optimization of single positions is based on the derivation of the optimal value of the corresponding variable that minimizes the Sum of Squared Errors between the approximated matrix X and prediction matrix UV . An optimal value for a position is such just at the time when it is modified, however, with an entire traversal of matrices U and M, previously optimized values might necessitate re-optimization. To this end, we implement an iterative algorithm that visits and optimizes all elements of U before and succes sively all elements of V , cycling row by row in the former and column by column in the latter until the gradient of the RMSE between two iterations drops under a certain value or when a heuristically-decided maximum of iterations is fulfilled. Assume that we substitute the elements of U with variables x and the elements of V with variables y
one at a time, we simplify the implementation by directly computing the optimal values of these ones (exploiting the formulas of page 346 of the referenced textbook). To sum up, we will cycle across all the values of U and update them. Then, we repeat the same on all values of M . Once both matrices have been optimized, we will compute their dot product, evaluate the RMSE value between X and UV and control if the ending criterion is reached. In case it is, we will exit the main loop and output the
prediction result.





