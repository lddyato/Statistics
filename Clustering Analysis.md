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
set.seed(1234)
par(mar = c(0, 0, 0, 0))
x <- rnorm(12, mean = rep(1:3, each = 4), sd = 0.2)
y <- rnorm(12, mean = rep(c(1, 2, 1), each = 4), sd = 0.2)
plot(x, y, col = "blue", pch = 19, cex = 2)
text(x + 0.05, y + 0.05, labels = as.character(1:12))
# Hierarchical clustering - dist
dataFrame <- data.frame(x = x, y = y)
dist(dataFrame) #lower triangular matrix
hClustering <- hclust(distxy) #merge points: single、complete、median、average
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
