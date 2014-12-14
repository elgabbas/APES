---
layout: page
title: Plotting in R
category: r
---

Plotting in R
=== 

A lot more examples in the links here https://github.com/biometry/APES/blob/master/R/R10-gettingStarted.md#cookbooks--how-to

# Simple standard plots

## Scatter plot

### Simple Scatterplot


```r
attach(iris) # remember to detach in the end 
head(iris)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 3          4.7         3.2          1.3         0.2  setosa
## 4          4.6         3.1          1.5         0.2  setosa
## 5          5.0         3.6          1.4         0.2  setosa
## 6          5.4         3.9          1.7         0.4  setosa
```

```r
plot(Petal.Length, Sepal.Length, main="Simple Scatterplot",xlab="Petal Length ",ylab="Sepal Length " , 
     pch=22, col="red", bg="blue",cex=3, lwd=2)

abline(lm(Sepal.Length~Petal.Length), col="red")
lines(lowess(Petal.Length, Sepal.Length), col="blue")
```

![plot of chunk unnamed-chunk-1](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-1.png) 

Where:	
 * Petal.Length = x	   
 * Sepal.Length = y		 
 * main = Main title	
 * xlab = title for the x axis	
 * ylab = title for the y axis
 * pch = type of symbol	
 * col = color of pch	
 * bg = background color of pch (from 21 to 25)	  
 * lwd = size of pch	


It is possible to add **fitting lines**:   

1.	Regresion line, where y is dependent of x (y~x).



2.	Lowess returns a list containing components x and y which give the coordinates of the smooth. 
		


By downloading the <b>car</b> package we can use more enhanced features


```r
library(car)
scatterplot(Sepal.Length~Petal.Length | Species, data=iris, boxplots= "x,y",
            xlab="Petal Length ",ylab="Sepal Length ",
            main="Enhanced Scatter Plot", labels=row.names(iris))
```

![plot of chunk unnamed-chunk-2](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-2.png) 

Where:
* `Sepal.Length~Petal.Length | Species` = the formula to plot by groups `(x ~ y | z)`. It could also be a formula of the type `x ~ y`.  
* `data` = dataframe   
* `boxplots` = boxplots for x and/or y   
* `main` = main title   
* `xlab` = title for the x axis	  
* `ylab` = title for the y axis   
* `labels` = labels for the points    


Other enhanced features include: boxplots, regresion lines, jitter factors, legend options, etc.


###Scaterplott Matrices


```r
pairs(~Sepal.Length+Sepal.Width+Petal.Length+Petal.Width,data=iris,
      main="Simple Scatterplot Matrix")
```

![plot of chunk unnamed-chunk-3](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-3.png) 

Where:   
* `~Sepal.Length+Sepal.Width+Petal.Length+Petal.Width` = Numeric vectors that represent the variables in the plot  
* `data` = the data frame
* `main` = main title   

With the **lattice** package it is possible to condicion our matrix on a factor (tree specie in this case)


```r
library(lattice)
super.sym <- trellis.par.get("superpose.symbol")  
splom(~iris[1:4], groups = Species, data = iris,  
      superpanel, key = list(title = "Varieties of Iris",  
                 columns = 3,   
                 points = list(pch = super.sym$pch[1:3],  
                               col = super.sym$col[1:3]),  
                 text = list(c("Setosa", "Versicolor", "Virginica"))))  
```

![plot of chunk unnamed-chunk-4](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-4.png) 

Where:   
•~iris[1:4] = the object we want to analyse (columns 1 to 4)  
•groups = which variable is going to be evaluated  
•data = dataframe   
•superpanel = function that sets up the splom display, by default as a scatterplot matrix   
•key = legend   
•text = labels for levels of the grouping variable   



We can also condicion our matrix on a factor with the <b>car</b> package.   
The advantage of this package is that we can include lowess and linear best fit lines,  boxplots, densities, or histograms in the principal diagonal, as well as rug plots in the margins of the cells.


```r
library(car)
scatterplot.matrix(~Sepal.Length+Sepal.Width+Petal.Length+Petal.Width|Species, data=iris,   
                   main="Three Species Options") 
```

```
## Warning: 'scatterplot.matrix' is deprecated.
## Use 'scatterplotMatrix' instead.
## See help("Deprecated") and help("car-deprecated").
```

![plot of chunk unnamed-chunk-5](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-5.png) 


With the <b>gclus</b> package we can rearrange the variables to represent those with higher correlations closer to the principal diagonal and to set up diferent colors depending of the correlation grade.  


```r
library(gclus)  
```

```
## Loading required package: cluster
```

```r
data <- iris[c(1,2,3,4)]    
data.corr <- abs(cor(data))  
data.color <- dmat.color(data.corr) 
# reorder variables so those with highest correlation  
# are closest to the diagonal  
data.order <- order.single(data.corr)   
cpairs(data, data.order, panel.colors=data.color, gap=.5,  
       main="Correlation Graph" )  
```

![plot of chunk unnamed-chunk-6](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-6.png) 
Where:   
•data = we get the data (columns 1,2,3,4 in this case)   
•data.corr = we get the correlations    
•data.color = get the colors depending of the correlation   
•data.order = to reorder the variables according to the correlation and proximity to the diagonal   
•cpairs = plotting the result     



### High Density Scatterplot

There are 2 options to plot whenever we are working with too many data points that overlap.  
The <b>hexbin</b> package creates a group of hexagonal cells and a count of points falling in each occupied cell. 

```r
library(hexbin)  
bin<-hexbin(Petal.Width ~ Petal.Length, xbins=100, xlab="Petal.Width",ylab="Petal.Length")  
plot(bin, main="Hexagonal Binning") 
```

![plot of chunk unnamed-chunk-7](https://raw.githubusercontent.com/biometry/APES/f96f52c54a5fab4e05a7b66f9acab3cd9316c4ed/R/R40-PlottingInR_files/figure-html/unnamed-chunk-26.png) 
Where:  
•xbins = number of hexagons across the x axis  


The other option is the <b>sunflowerplot</b> function.


```r
sunflowerplot(Petal.Width ~ Petal.Length, data = iris,  
              cex = .5, cex.fact = .8, size = .15,   
              xlab="Petal.Width",ylab="Petal.Length", main= "Sunflower Plot")  
```

![plot of chunk unnamed-chunk-8](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-8.png) 
Where:   
•cex = size of the center points  
•cex.fact = size of the center points where there are flower leaves   
•size = size of the flower leaves  


### Three-dimensional

we need the <b>scatterplot3d</b> package to represent 3D scatterplots.


```r
library(scatterplot3d)  
iris3d<-scatterplot3d(Sepal.Length,Sepal.Width,Petal.Length, pch=18, highlight.3d=TRUE,  
              type="h", main="3D Iris")  
reg.plane <- lm(Petal.Length ~ Sepal.Length+Sepal.Width)   
iris3d$plane3d(reg.plane)  
```

![plot of chunk unnamed-chunk-9](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-9.png) 
Where:   
•pch = type of symbol   
•highlight.3d = the points will are colored according to the y coordinates     
•type = "p" for points, "l" for lines, "h" for vertical lines   
•plane3d = we draw a regression plane


It is possible to spin our 3d model with the mouse using the <b>rgl</b> package or the <b>Rcmdr</b> package.  


```r
library(rgl)   
```

```
## 
## Attaching package: 'rgl'
## 
## The following object is masked from 'package:car':
## 
##     identify3d
```

```r
plot3d(Sepal.Length,Sepal.Width,Petal.Length, col="blue", size=4)  
```


```r
library(Rcmdr)  
```

```
## Loading required package: splines
## The Commander GUI is launched only in interactive sessions
```

```r
scatter3d(Sepal.Length,Sepal.Width,Petal.Length)  
```

```
## Loading required package: mgcv
## Loading required package: nlme
## This is mgcv 1.7-26. For overview type 'help("mgcv-package")'.
```


## Line plots

we will represent the diferent <b>types of lines</b> in one plot with random x and y data:

```r
x <- c(1:5); y <- x 
par(pch=22, col="blue") 
par(mfrow=c(2,4)) 
opts = c("p","l","o","b","c","s","S","h") 
for(i in 1:length(opts)){ 
  heading = paste("type=",opts[i]) 
  plot(x, y, type="n", main=heading) 
  lines(x, y, type=opts[i])}
```

![plot of chunk unnamed-chunk-12](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-12.png) 
Where:   
•mfrow = with this option we represent all the plots in the same page. To go back to only one plot per page, we write par(mfrow=c(1,1))    
* types
  * p = points  
  * l = lines  
  * o = points & lines overplotted  
  * b = points linked by lines  
  * c = intermittent lines  
  * s, S = stair steps  
  * h = vertical lines  
  * n = nothing   
* plot = we create a plot where we will add the lines  
* lines = lines to add to the created plot  


An example with our iris data:   

```r
# convert factor to numeric for convenience 
iris$Species <- as.numeric(iris$Species) 
ntrees <- max(iris$Species)

# get the range for the x and y axis 
xrange <- range(iris$Sepal.Width) 
yrange <- range(iris$Sepal.Length) 

# set up the plot 
plot(xrange, yrange, type="n", xlab="Sepal Width",
     ylab="Sepal Length " ) 
colors <- rainbow(ntrees) 

# add lines 
for (i in 1:ntrees) { 
  species <- subset(iris, Species==i) 
  lines(species$Sepal.Width,species$Sepal.Length, type="b", lwd=1.5,
        lty=1:ntrees, col=colors[i], pch=1:ntrees)} 

# add title and legend
title("Iris Sepals")
legend(xrange[1], yrange[2], 1:ntrees, cex=0.8, col=colors,
       pch=1:ntrees, lty=1:ntrees, title="Species")
```

![plot of chunk unnamed-chunk-13](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-13.png) 



## Bar plots

### Simple Bar Plot


```r
counts <- table(iris$Petal.Length)
barplot(counts, main="Iris Distribution", 
        xlab="Petal Length")
```

![plot of chunk unnamed-chunk-14](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-14.png) 

It is possible to plot the bars horizontal by adding horiz=TRUE to the code


### Stacked Bar Plot

If we want to represent different values in each bar


```r
counts <- table(iris$Species, iris$Petal.Length)  
barplot(counts, main="iris species distribution by petal leghts",
                    xlab="Petal Length",  col=c("darkblue","red", "green"),
                    legend = rownames(counts))
```

![plot of chunk unnamed-chunk-15](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-15.png) 

We can represent each value in a bar instead of stacked by adding beside=TRUE to the code


## Pie charts

Don't do them! Use Bar plots instead as it is easier to appreciate the differences between bars. But if you have to use them:


```r
#set up the variables
Spcs <- table(iris$Species) 
Spcs
```

```
## 
##  1  2  3 
## 50 50 50
```

```r
lbls <- c("Setosa", "Versicolor", "Virginica")
# add percentages to the pie
pct <- round(Spcs/sum(Spcs)*100)
lbls <- paste(lbls, pct)
lbls <- paste(lbls,"%",sep="")
# plot the chart
pie(Spcs,labels = lbls,
main="Pie Chart of Countries")
```

![plot of chunk unnamed-chunk-16](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-16.png) 


With the <b>plotrix</b> package we can create 3d pie charts

## Histograms

Not recommended as its accuracy depends of the number of bins used

### Simple Histogram


```r
Petal<-iris$Petal.Length				
hist(Petal)		
```

![plot of chunk unnamed-chunk-17](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-17.png) 


```r
hist(Petal,freq=FALSE, breaks=15,col="green")		
```

![plot of chunk unnamed-chunk-18](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-18.png) 
* freq = to show densities instead of frequencies		
* breaks = we can set up the number of bins		


We can add a <b>normal curve</b> to our histogram


```r
h<-hist(Petal, breaks=15, col="green", xlab="Petal Length", 		
        main="Histogram with Normal Curve") 		
xfit<-seq(min(Petal),max(Petal),length=40) 		
yfit<-dnorm(xfit,mean=mean(Petal),sd=sd(Petal)) 		
yfit <- yfit*diff(h$mids[1:2])*length(Petal) 		
lines(xfit, yfit, col="blue", lwd=2)		
```

![plot of chunk unnamed-chunk-19](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-19.png) 

## Density plots


```r
dens <- density(iris$Petal.Length) 		
plot(dens, main="Kernel Density of Petal Length") 		
polygon(dens, col="pink", border="purple")		
```

![plot of chunk unnamed-chunk-20](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-20.png) 
* polygon = to color the density plot


We can compare different Kernel density groups with the <b> sm package </b>


```r
library(sm)
```

```
## Package 'sm', version 2.2-5.4: type help(sm) for summary information
```

```r
# create value labels 
spec.f <- factor(Species,
                labels = c("setosa", "versicolor", "virginica")) 
# plot densities 
sm.density.compare(Petal.Length, Species, xlab="Petal Length")
title(main="Petal Length by Specie")
# add legend 
colfill<-c(2:(2+length(levels(spec.f)))) 
legend("topright", levels(spec.f), fill=colfill)
```

![plot of chunk unnamed-chunk-21](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-21.png) 

# Others

## Box plots

```r
boxplot(Petal.Length~Species,data=iris, main="Species Petal Length", 		
        xlab="Species", ylab="Petal Length")
```

![plot of chunk unnamed-chunk-22](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-22.png) 

By adding varwidth=TRUE to the formula we can make the boxplot widths proportional to the square root of the samples sizes. 			
By adding horizontal=TRUE we reverse the axis orientation.

### Notched Boxplot

It is also possible to create a notched Boxplot of a variable against 2 crossed factors.		
This time we used the example of the Carbon Dioxide Uptake in Grass Plants against Type and treatment



```r
boxplot(uptake~Type*Treatment, data=CO2, notch=TRUE, 		
        col=(c("gold","darkgreen")),		
        main="Carbon Dioxide Uptake in Grass Plants", xlab="Type and treatment")		
```

```
## Warning: some notches went outside hinges ('box'): maybe set notch=FALSE
```

![plot of chunk unnamed-chunk-23](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-23.png) 

### Violin Plot

With a violin plot (<b>vioplot</b> package) we can visualise easily the density data.		


```r
iris$Species <- as.numeric(iris$Species) 		
library(vioplot)		
x1 <- iris$Petal.Length[iris$Species==1]		
x2 <- iris$Petal.Length[iris$Species==2]		
x3 <- iris$Petal.Length[iris$Species==3]		
vioplot(x1, x2, x3, names=c("setosa", "versicolor", "virginica"), 		
        col="gold")		
title("Violin Plots of Petal Length")		
```

![plot of chunk unnamed-chunk-24](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-24.png) 

### Bagplot

Witht the <b>aplpack</b> package we can create a bivariate (2D) boxplot. 50% of the points are in the bag.


```r
library(aplpack)			
```

```
## Loading required package: tcltk
## 
## Attaching package: 'tcltk'
## 
## The following objects are masked from 'package:Rcmdr':
## 
##     tclvalue, tkfocus
```

```r
bagplot(Petal.Length, Sepal.Length, xlab="Petal Length ",ylab="Sepal Length ",			
        main="Iris Bagplot ")		
```

![plot of chunk unnamed-chunk-25](https://github.com/biometry/APES/blob/e6227e751cd2cc8c7d2594edef5dabee9ba15afb/R/R40-PlottingInR_files/figure-html/unnamed-chunk-25.png) 





```r
detach(iris)
```



# Understanding graphics 


## Colors

http://www.mepheoscience.com/colourful-ecology-part-1-extracting-colours-from-an-image-and-selecting-them-using-community-phylogenetics-theory/



