# Clustering Analysis
Clustering organizes things that are close into groups 
1. How do we define close?
2. How do we group things?
3. How do we visualize the grouping?
4. How do we interpret the grouping?


## Hierarchical Clustering
* An agglomerative approach
 + Find closest two things
 + Put them together
 + Find next closest
* Requires
 + A defined distance
 + A merging approach
* Produces
 + A tree showing how close things are to each other
 
**How do we define close?**
* Most important setp
 + Garbage in -> garbage out
* Distance or similarity
 + Continuous - euclidean distance
 + Continuous - correlation similarity
 + Binary - Manhattan distance
* Pick a distance/similarity that makes sense for your problem

```r
dist(x, method = "euclidean", diag = FALSE, upper = FALSE, p = 2)
```
This function computes and returns the distance matrix computed by using the specified distance measure to compute the distances between the rows of a data matrix.
* x	: a numeric matrix, data frame or "dist" object.
* method: "euclidean", "maximum", "manhattan", "canberra", "binary" or "minkowski". 
* diag: logical value indicating whether the diagonal of the distance matrix should be printed by print.dist.
* upper: logical value indicating whether the upper triangle of the distance matrix should be printed by print.dist.
* p: The power of the Minkowski distance.
```r
set.seed(1234)
par(mar = c(0, 0, 0, 0))
x <- rnorm(12, mean = rep(1:3, each = 4), sd = 0.2)
y <- rnorm(12, mean = rep(c(1, 2, 1), each = 4), sd = 0.2)
plot(x, y, col = "blue", pch = 19, cex = 2)
text(x + 0.05, y + 0.05, labels = as.character(1:12))
# Hierarchical clustering - dist
dataFrame <- data.frame(x = x, y = y)
dist(dataFrame) #lower triangular matrix
hClustering <- hclust(distxy, method="complete") #merge points: single、complete、median、average, Ward
plot(hClustering)
```
**Prettier dendrograms**
```r
myplclust <- function(hclust, lab = hclust$labels, lab.col = rep(1, length(hclust$labels)),
hang = 0.1, ...) {
## modifiction of plclust for plotting hclust objects *in colour*! Copyright
## Eva KF Chan 2009 Arguments: hclust: hclust object lab: a character vector
## of labels of the leaves of the tree lab.col: colour for the labels;
## NA=default device foreground colour hang: as in hclust & plclust Side
## effect: A display of hierarchical cluster with coloured leaf labels.
y <- rep(hclust$height, 2)
x <- as.numeric(hclust$merge)
y <- y[which(x < 0)]
x <- x[which(x < 0)]
x <- abs(x)
y <- y[order(x)]
x <- x[order(x)]
plot(hclust, labels = FALSE, hang = hang, ...)
text(x = x, y = y[hclust$order] - (max(hclust$height) * hang), labels = lab[hclust$order],
col = lab.col[hclust$order], srt = 90, adj = c(1, 0.5), xpd = NA, ...)
}
```
**Pretty dendrograms**
```r
dataFrame <- data.frame(x = x, y = y)
distxy <- dist(dataFrame)
hClustering <- hclust(distxy)
myplclust(hClustering, lab = rep(1:3, each = 4), lab.col = rep(1:3, each = 4))
```

**heatmap(): Runs a hierarchical cluster analysis on the rows of the table and on the columns of the table.**
```r
dataFrame <- data.frame(x = x, y = y)
set.seed(143)
dataMatrix <- as.matrix(dataFrame)[sample(1:12), ]
heatmap(dataMatrix) 
```
**Notes and further sources**
* Gives an idea of the relationships between variables/observ
* The picture may be unstable
 + Change a few points
 + Have different missing values
 + Pick a different distance
 + Change the merging strategy
 + Change the scale of points for one variableBut it is deterministic
* Choosing where to cut isn't always obvious
* Should be primarily used for exploration
* link - Rafa's Distances and Clustering Video
* link - Elements of statistical learning

## K-Means Clustering
* A partioning approach
 + Fix a number of clusters
 + Get "centroids" of each cluster
 + Assign things to closest centroid
 + Reclaculate centroids
* Requires
 + A defined distance metric
 + A number of clusters
 + An initial guess as to cluster centroids
* Produces
 + Final estimate of cluster centroids
 + An assignment of each point to clusters
 
```r
set.seed(1234)
par(mar = c(0, 0, 0, 0))
x <- rnorm(12, mean = rep(1:3, each = 4), sd = 0.2)
y <- rnorm(12, mean = rep(c(1, 2, 1), each = 4), sd = 0.2)
plot(x, y, col = "blue", pch = 19, cex = 2)
text(x + 0.05, y + 0.05, labels = as.character(1:12))
# K-means clustering - starting centroids
dataFrame <- data.frame(x, y)
kmeansObj <- kmeans(dataFrame, centers = 3)
names(kmeansObj)
kmeansObj$cluster
par(mar = rep(0.2, 4))
plot(x, y, col = kmeansObj$cluster, pch = 19, cex = 2)
points(kmeansObj$centers, col = 1:3, pch = 3, cex = 3, lwd = 3)
```
**heatmaps()**
```r
set.seed(1234)
dataMatrix <- as.matrix(dataFrame)[sample(1:12), ]
kmeansObj2 <- kmeans(dataMatrix, centers = 3)
par(mfrow = c(1, 2), mar = c(2, 4, 0.1, 0.1))
image(t(dataMatrix)[, nrow(dataMatrix):1], yaxt = "n")
image(t(dataMatrix)[, order(kmeansObj$cluster)], yaxt = "n")
```

**Notes and further sources**
* K-means requires a number of clusters
 + Pick by eye/intuition
 + Pick by cross validation/information theory, etc
 + Determining the number of clusters 
* K-means is not deterministic
 + Different # of clusters
 + Different number of iterations
* Rafael Irizarry's Distances and Clustering Video
* Elements of statistical learning

