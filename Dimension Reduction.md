# Principle Components Analysis and Singular Value Decomposition
* Principle Components Analysis (PCA)
* Singular Value Decomposition (SVD)
PCA and SVD are used in both the exploratory phase and the more formal modelling stage of analysis. 
```r
#Matrix data
set.seed(12345)
par(mar = rep(0.2, 4))
dataMatrix <- matrix(rnorm(400), nrow = 40) # mean 0 and standard deviation 1; 10 columns and 40 rows
image(1:10, 1:40, t(dataMatrix)[, nrow(dataMatrix):1])
#Cluster the data
par(mar = rep(0.2, 4))
heatmap(dataMatrix)
```
What if we add a pattern?
```r
set.seed(678910)
for (i in 1:40) {
  # flip a coin
  coinFlip <- rbinom(1, size = 1, prob = 0.5)
  # if coin is heads add a common pattern to that row
  if (coinFlip) {
    dataMatrix[i, ] <- dataMatrix[i, ] + rep(c(0, 3), each = 5)
  }
}#in rows affected by the coin flip, the 5 left columns will still have a mean of 0 but the right 5 columns will have a mean closer to 3.
source("addPatt.R", local=TRUE)
#the data
par(mar = rep(0.2, 4))
image(1:10, 1:40, t(dataMatrix)[, nrow(dataMatrix):1])
#the clustered data
par(mar = rep(0.2, 4))
heatmap(dataMatrix)
#patterns in row and columns
hh <- hclust(dist(dataMatrix))
dataMatrixOrdered <- dataMatrix[hh$order, ]
> svd1 <- svd(dataMatrix)
> svd1$v[,1]
 [1] -0.01269600  0.11959541  0.03336723  0.09405542 -0.12201820 -0.43175437
 [7] -0.44120227 -0.43732624 -0.44207248 -0.43924243
> svd1$d
 [1] 12.458121  7.779798  6.732595  6.301878  5.860013  4.501826  3.921267  2.973909
 [9]  2.401470  2.152848

par(mfrow = c(1, 3))
image(t(dataMatrixOrdered)[, nrow(dataMatrixOrdered):1])
plot(rowMeans(dataMatrixOrdered), 40:1, , xlab = "Row Mean", ylab = "Row", pch = 19)
plot(colMeans(dataMatrixOrdered), xlab = "Column", ylab = "Column Mean", pch = 19)
#The middle display shows the mean of each of the 40 rows (along the x-axis). The
 rows are shown in the same order as the rows of the heat matrix on the left. The
 rightmost display shows the mean of each of the 10 columns. Here the column
 numbers are along the x-axis and their means along the y.
 
```
Related Problems
You have multivariate variables X1, … , Xn, so X1 = (X11, … , X1m)
* Find a new set of multivariate variables that are uncorrelated and explain as much variance as
possible.
* If you put all the variables together in one matrix, find the best matrix created with fewer variables
(lower rank) that explains the original data.   

The first goal is statistical and the second goal is data compression.

Related solutions - PCA/SVD
* SVD
If X is a matrix with each variable in a column and each observation in a row then the SVD is a
"matrix decomposition"
X = UDV^T   
where the columns of U are orthogonal (left singular vectors), the columns of V are orthogonal (right
singular vectors) and D is a diagonal matrix (singular values).
* PCA
The principal components are equal to the right singular values if you first scale (subtract the mean,
divide by the standard deviation) the variables.
```r
#A simple example matrix
> mat
     [,1] [,2] [,3]
[1,]    1    2    3
[2,]    2    5    7

> svd(mat)
$d  
[1] 9.5899624 0.1806108

$u
           [,1]       [,2]
[1,] -0.3897782 -0.9209087
[2,] -0.9209087  0.3897782

$v
           [,1]       [,2]
[1,] -0.2327012 -0.7826345
[2,] -0.5614308  0.5928424
[3,] -0.7941320 -0.1897921

# store the diagonal entries d in a diagonal matrix, diag
# store u and v in the variables matu and matv

> matu %*% diag %*% t(matv)  # equals to mat, this type of decomposition is NOT unique
     [,1] [,2] [,3]
[1,]    1    2    3
[2,]    2    5    7

> svd(scale(mat))
$d
[1] 1.732051 0.000000

$u
           [,1]      [,2]
[1,] -0.7071068 0.7071068
[2,]  0.7071068 0.7071068

$v
          [,1]       [,2]
[1,] 0.5773503 -0.5773503
[2,] 0.5773503  0.7886751
[3,] 0.5773503 -0.2113249

> prcomp(scale(mat))
Standard deviations:
[1] 1.732051 0.000000

Rotation:
           PC1        PC2
[1,] 0.5773503 -0.5773503
[2,] 0.5773503  0.7886751
[3,] 0.5773503 -0.2113249
#Notice that the principal components of the scaled matrix, shown in the Rotation
 component of the prcomp output, ARE the columns of V, the right singular values.
 Thus, PCA of a scaled matrix yields the V matrix (right singular vectors) of the
 same scaled matrix.
```
```r
> head(constantMatrix) # 40 by 10 matrix
     [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10]
[1,]    0    0    0    0    0    1    1    1    1     1
[2,]    0    0    0    0    0    1    1    1    1     1
[3,]    0    0    0    0    0    1    1    1    1     1
[4,]    0    0    0    0    0    1    1    1    1     1
[5,]    0    0    0    0    0    1    1    1    1     1
[6,]    0    0    0    0    0    1    1    1    1     1

> svd2 <- svd(constantMatrix)
> svd2$d
 [1] 1.414214e+01 1.293147e-15 2.515225e-16 8.585184e-31 9.549693e-32 3.330034e-32
 [7] 2.022600e-46 4.362170e-47 1.531252e-61 0.000000e+00
```


