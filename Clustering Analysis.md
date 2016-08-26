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
dist(dataFrame) #lower triangular matrix，By default dist uses Euclidean distance as its metric,
hClustering <- hclust(distxy, method="complete") #merge points: single、complete（default）、median、average, Ward
plot(hClustering)
plot(as.dendrogram(hClustering))# The essentials are the same, but the labels are missing and the leaves (original points) are all printed at the same level.
abline(h=1.5, col="blue") #3clusters
abline(h=0.4, col="red")#5clusters
abline(h=0.05, col="green")#12clusters
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
Larger values were represented by small dark gray or black squares (pixels) and smaller values by lighter squares.
<http://sebastianraschka.com/Articles/heatmaps_in_r.html#clustering>
```r
dataFrame <- data.frame(x = x, y = y)
set.seed(143)
dataMatrix <- as.matrix(dataFrame)[sample(1:12), ]
heatmap(dataMatrix) 
heatmap(dataMatrix,col=cm.colors(25))
------------------
#dataset mtcars
heatmap(mt)
mt
#                  mpg cyl  disp  hp drat    wt
#Dodge Challenger 15.5   8 318.0 150 2.76 3.520
#AMC Javelin      15.2   8 304.0 150 3.15 3.435
#Camaro Z28       13.3   8 350.0 245 3.73 3.840
#Pontiac Firebird 19.2   8 400.0 175 3.08 3.845
#Fiat X1-9        27.3   4  79.0  66 4.08 1.935
#Porsche 914-2    26.0   4 120.3  91 4.43 2.140
#Lotus Europa     30.4   4  95.1 113 3.77 1.513
#Ford Pantera L   15.8   8 351.0 264 4.22 3.170
#Maserati Bora    15.0   8 301.0 335 3.54 3.570
#Volvo 142E       21.4   4 121.0 109 4.11 2.780

plot(denmt) #dendrogram is the one displayed at the side of the heat map
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
#Set k=3
> cmat #stored 3 positions of centroids for each cluster in a 2 by 3 matrix cmat.
     [,1] [,2] [,3]
[1,]    1  1.8  2.5
[2,]    2  1.0  1.5
points(cx, cy, col=c("red", "orange", "purple"), pch=3, cex=2, lwd=2)
#The distance between each point and one centroid means 12 distances have to be calculated for each centroid. This has to be done for all 3 centroids.




 
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
kmeansObj
K-means clustering with 3 clusters of sizes 4, 4, 4

#Cluster means:
#          x         y
#1 2.8534966 0.9831222
#2 0.8904553 1.0068707
#3 1.9906904 2.0078229

#Clustering vector:
# [1] 2 2 2 2 3 3 3 3 1 1 1 1

#Within cluster sum of squares by cluster:
#[1] 0.03298027 0.34188313 0.34732441
# (between_SS / total_SS =  93.6 %)

#Available components:

#[1] "cluster"      "centers"      "totss"        "withinss"     "tot.withinss"
#[6] "betweenss"    "size"         "iter"         "ifault"      

names(kmeansObj)
kmeansObj$cluster
kmeansObj$iter  # how many iterations the algorithm went through
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

