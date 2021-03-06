Cross-validation techniques for model evaluation
================
Carsten F. Dormann
01 Mar 2017

-   [Introduction](#introduction)
-   [Types of cross-validation](#types-of-cross-validation)
-   [Measures of evaluation](#measures-of-evaluation)
-   [The problem of non-independence of randomly chosen hold-outs](#the-problem-of-non-independence-of-randomly-chosen-hold-outs)
-   [An example](#an-example)
    -   [Jackknifed estimate errors and LOOCVed prediction errors](#jackknifed-estimate-errors-and-loocved-prediction-errors)
    -   [*k*-fold cross-validation](#k-fold-cross-validation)
    -   [Repeated sub-sampling validation](#repeated-sub-sampling-validation)
-   [Take-home message](#take-home-message)

Introduction
============

The bias-variance trade-off shows that the more complexity you allow into a statistical model, the more variance is explained, at the expense of bias in predictions made by an overfit model. So we cannot rely the self-reporting of a fitted model when it comes to prediction error. For that, we need independent data, which typically do not have.

![Fig. 1.](biasvariance.png)

The bias-variance trade-off: you can be too simple, and too complex. (Figure stolen from Scott Fortmann-Roe's [blog](http://scott.fortmann-roe.com/docs/BiasVariance.html))

Types of cross-validation
=========================

In cross-validation (CV for short), some part of the data are held back when the model is fitted, and used for evaluating the error of the fitted model. The hold-out is called the test, the other part the training data set.

Cross-validation approaches differ in how many of the data are allocated to test/training, and how often the CV itself is repeated.

The more data are set apart for testing, the fewer are available for training, thus yielding poorer models with higher prediction error than the full model has. On the other hand, leaving out only one data point is hardly a challenge for the predictive ability of a model. There is no good solution to this trade-off, apart from having truely independent test data. Also check the corresponding [wikipedia page](https://en.wikipedia.org/wiki/Cross-validation_%28statistics%29).

1.  **Leave-one-out-cross-validation** (LOOCV) leaves out only 1 data point, and does that for each data point in turn. Thus, LOOCV requires *N* model evaluations (*N* is the number of data points), which is costly for large *N*. The advantage is that the procedure delivers exactly the same results every time, because all possible options are being evaluated.

    There is a subtle difference between LOOCV and the jackknife: the jackknife computes a statistic *from the training data*, while LOOCV computes the statistics *from the test data*. For example, we can use the jackknife to compute the standard error of a linear model estimate, but we use LOOCV to compute the prediction error of this model.

    LOOCV is a classic. It has been shown (Stone 1977 J Roy Stat Soc B) to be the quantity that the AIC actually approximates, and both approximate the Kullback-Leibler-divergence of a model. Thus LOOCV is a must-know.
2.  **Leave-*p*-out-CV** does the same, but for more than 1 data point (i.e. *p*). There are many possibilities if *N* and *p* are large, e.g. for 100 data points and *p* = 5 we have 75 million options of keeping 5 data points as test. Obviously, we can only evaluate a few of those, and hence leave-*p*-out cross-validation is typically not exhaustive.
3.  ***k*-fold cross-validation** is phrasing the previous point differently. Instead of putting *k* data points into the test, we split the entire data set into *k* partitions, the so-called folds, and keep one fold for testing after fitting the model to the other folds. Thus, we evaluate *k* models on each of the *k* folds not used. Typical values for *k* are 5-10, with a slight preference towards 10.
4.  **Repeated sub-sampling validation** (aka Monte Carlo-CV) splits the data once into test and train (whatever proportion you deem suitable, e.g. 30/70%), evaluatates the trained model on the test hold-out, and then repeats this procedure many times (say, 1000 times). The caveat is that in this case some data points happen to be chosen several, other never for the test set. As the number of repetitions approaches infinity, we get the same values as for leave-*p*-out-CV.

Measures of evaluation
======================

Since the aim is to quantify prediction error, root mean squared error is the most commonly used measure of fit to test data:
$$RMSE = \\sqrt{\\frac{1}{N}\\sum\_{i=1}^N{(y\_i-\\hat{f}\_i)^2}},$$
 where $\\hat{f}\_i$ is the model prediction to test data point *i*, and *y*<sub>*i*</sub> is the observed value for this test data point.

In some fields, the median absolute deviation is used instead (which is unsurprisingly the median of $| y\_i-\\hat{f}\_i |$). This measure is more robust (i.e. less affected by extreme values) and hence often used when deviations are very skewed (e.g. river discharge). This is not the place to discuss evaluation measures.

The problem of non-independence of randomly chosen hold-outs
============================================================

In the above, I carefully avoided any mentioning of *how* test and train data are selected, thereby suggesting that this should be done randomly. That is indeed the standard statistical approach, because we assume that our data are independent (otherwise we weren't allowed to do a standard linear regression or alike anyway).

In many ecological (and other) data, this assumption is violated: in time series, we notice temporal autocorrelation (points closer together are more alike), in space we have the equivalent spatial autocorrelation, in phylogenetic data we have phylogenetic non-independence. Finally, in nested designs, we use random effects to address non-independence of data. In short: structured data are typically non-independent. That also means that randomly splitting them into test/train (or folds) makes the test similar to the train! To achieve actual independence of the test data, we need to resort to **block cross-validation**.

That happens to be the topic of a review we wrote recently (Roberts et al. 2017 Ecography), and which I will present in a separate talk. I mention here only the main idea: split the data in such a way that test and train are "futher apart" than the range of the non-independence. That may not always be possible, in which case *any* CV will be too optimistic.

An example
==========

Let's fit body and brain weight as predictors to how many hours mammals are sleeping per day.

``` r
library(faraway)
data(mammalsleep)
fm <- lm(sleep ~ log10(body) * log10(lifespan), data=mammalsleep)
summary(fm)
```


    Call:
    lm(formula = sleep ~ log10(body) * log10(lifespan), data = mammalsleep)

    Residuals:
        Min      1Q  Median      3Q     Max 
    -7.6491 -2.2056  0.3238  2.6275  7.0193 

    Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
    (Intercept)                  12.9555     1.8364   7.055 4.92e-09 ***
    log10(body)                   1.6205     1.3070   1.240  0.22082    
    log10(lifespan)              -0.7723     1.7560  -0.440  0.66197    
    log10(body):log10(lifespan)  -2.5357     0.9354  -2.711  0.00917 ** 
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 3.797 on 50 degrees of freedom
      (8 observations deleted due to missingness)
    Multiple R-squared:  0.3854,    Adjusted R-squared:  0.3485 
    F-statistic: 10.45 on 3 and 50 DF,  p-value: 1.897e-05

How well does that model predict to a mammal not included in the data set? The self-reported error is only a modest 3.797 anyway. But is even this number reliable?

Jackknifed estimate errors and LOOCVed prediction errors
--------------------------------------------------------

``` r
nrow(mammalsleep)
```

    [1] 62

``` r
coef.mat <- matrix(NA, ncol=4, nrow=62)
RMSE <- numeric(62)
for (i in 1:62){
  fmcv <- lm(sleep ~ log10(body) * log10(lifespan), data=mammalsleep[-i,])
  coef.mat[i, ] <- coef(fmcv)
  predscv <- predict(fmcv, newdata=mammalsleep[i,])
  RMSE[i] <- sqrt(mean((mammalsleep$sleep[i] - predscv)^2))
  rm(fmcv, predscv)
}
jackMean <- colSums(coef.mat) / (62 - 4 - 1) # 4 NAs
jackSD <- sqrt((62 - 4 - 1) / (62-4) * colSums((coef.mat - matrix(jackMean, nrow=62, ncol=4, byrow=T))^2)) # see https://en.wikipedia.org/wiki/Jackknife_resampling
signif(jackMean, 4)
```

    [1] 14.0900  1.7600 -0.8421 -2.7560

``` r
signif(jackSD, 4)
```

    [1] 9.220 1.942 2.353 2.006

Compare these values with the estimates given in the model `fm`. The (*n* − 1) times the difference between the jackknife mean and the full model estimates is a measure of the estimation bias of the model.

Note that while the estimates are similar, the standard errors are substantially larger. The reason is that the lm makes large-sample assumptions, while the jackknife does not.

``` r
mean(RMSE, na.rm=T) # note that there are 4 missing values
```

    [1] 3.239387

This value is even lower than the self-reported 3.797.

*k*-fold cross-validation
-------------------------

We shall use a 6-fold cross-validation here, because we have 62 data points (minus 4 NAs). Again, this is somewhat arbitrary, and we could just as well use 10-fold CV.

``` r
set.seed(1)
folds <- sample(rep(1:6, len=62))
plot(sleep ~ lifespan, data=mammalsleep, pch=as.character(folds), log="x", cex=1.5, las=1)
```

<img src="CrossValidationLecture_files/figure-markdown_github/unnamed-chunk-4-1.png" style="display: block; margin: auto;" /> Note that fold 6 has 4 of the highest 5 values. That may affect the CV, because when fold 6 is omitted, the range is shorter. We'll see.

``` r
RMSE6fold <- numeric(6)
for (k in 1:6){
  fmcv <- lm(sleep ~ log10(body) * log10(lifespan), data=mammalsleep[which(folds != k),])
  predscv <- predict(fmcv, newdata=mammalsleep[which(folds==k),])
  RMSE6fold[k] <- sqrt(mean((mammalsleep$sleep[which(folds==k)] - predscv)^2, na.rm=T))
  rm(fmcv, predscv)
}
round(RMSE6fold, 3)
```

    [1] 4.094 4.109 4.001 3.556 4.704 3.899

So, fold 6 is not particularly apparent.

``` r
mean(RMSE6fold)
```

    [1] 4.060673

The cross-validated RMSE is somewhat higher than the self-reported.

Repeated sub-sampling validation
--------------------------------

``` r
reps <- 100
trainProportion <- 0.7
RMSE100fold <- numeric(reps)
for (k in 1:reps){
  trainSize <- floor(trainProportion * nrow(mammalsleep))
  CVindex <- sample(nrow(mammalsleep)) # shuffle row numbers
  fmcv <- lm(sleep ~ log10(body) * log10(lifespan), data=mammalsleep[CVindex[1:trainSize],])
  predscv <- predict(fmcv, newdata=mammalsleep[CVindex[(trainSize+1):nrow(mammalsleep)],])
  RMSE100fold[k] <- sqrt(mean((mammalsleep$sleep[CVindex[(trainSize+1):nrow(mammalsleep)]] - predscv)^2, na.rm=T))
  rm(fmcv, predscv)
}
#hist(RMSE100fold)
mean(RMSE100fold)
```

    [1] 4.05594

``` r
sd(RMSE100fold)
```

    [1] 0.5489808

As we see, both fold-based CV-approaches yield much larger RMSE, which can be attributed to the substantially lower sample size when fitting the model.

This difference becomes smaller as the sample size increases.

Take-home message
=================

Self-reported prediction error can be severely optimistic, due to overfitting ("fitting into the noise"). Cross-validation techniques try to overcome this deficiency by evaluating the model on data not used for training (=fitting) the model. For small data sets, withholding some data from the training may incur a severe cost: the model is underfitted, and hence generalises poorly. For larger sample sizes (&gt; a few hundred), all cross-validation techniques should work fine.

Be aware of structure in the data! This will require **non-random hold-outs** for testing, a topic not covered here.
