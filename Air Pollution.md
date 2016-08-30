# EDA case study - Fine Particulate Matter Air Pollution in the United States
<http://aqsdr1.epa.gov/aqsweb/aqstmp/airdata/download_files.html#Meta>

```r
less RD_501_88101__1999_0.txt # take a look at the data
grep ^RC RD_501_88101__1999_0.txt
grep ^RC RD_501_88101__2012_0.txt
pm0 <- read.table("grep ^RC RD_501_88101__1999_0.txt", comment.char = "#", header = FALSE,)
