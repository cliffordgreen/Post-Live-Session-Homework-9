Part 1
------

the goal for the first portion of the assignment is to analyze and
interpret time series data. we were assigned the visitors portion of the
provided dataset.

A. Plot the time series. Can you identify seasonal fluctuations and/or a
trend?

    library(fpp)

    ## Loading required package: forecast

    ## Warning: package 'forecast' was built under R version 3.2.5

    ## Loading required package: zoo

    ## Warning: package 'zoo' was built under R version 3.2.5

    ## 
    ## Attaching package: 'zoo'

    ## The following objects are masked from 'package:base':
    ## 
    ##     as.Date, as.Date.numeric

    ## Loading required package: timeDate

    ## This is forecast 7.1

    ## Loading required package: fma

    ## Warning: package 'fma' was built under R version 3.2.5

    ## Loading required package: tseries

    ## Warning: package 'tseries' was built under R version 3.2.5

    ## Loading required package: expsmooth

    ## Loading required package: lmtest

    data(books)
    data(ukcars)
    data(visitors)
    plot(visitors, type="l")

![](Post_Live_Session_9_files/figure-markdown_strict/unnamed-chunk-1-1.png)<!-- -->

Looking at the data above, there does appear to be cyclical trends every
year. The visitors is continuously increasing and decreasing. So we can say
that the data is cyclical

B. Calculating the trend-cycle and seasonal indices through classical
decompression

    fitd <- decompose(visitors)
    plot(fitd)

![](Post_Live_Session_9_files/figure-markdown_strict/unnamed-chunk-2-1.png)<!-- -->

C. Do the results support the graphical interpretation from part (a)?

Trend shows consistent increase, seasonal shoes peaks and dips during
seasons. Lets see what the seasonal adj data looks like

D.Compute and plot the seasonally adjusted data.

    seasonadj <- seasadj(fitd)
    plot(seasonadj, type="l")

![](Post_Live_Session_9_files/figure-markdown_strict/unnamed-chunk-3-1.png)<!-- -->

This is very interesting to look at, its essentially the attendance data without
the normal cyclical trends.

E. Change one observation to be an outlier (e.g., add 500 to one
observation), and recompute the seasonally adjusted data. What is the
effect of the outlier?

    visitors2 <- ts(c(visitors[1:54], visitors[55]+200, visitors[56:240]),start=c(1978,1),frequency = 12)
    plot(visitors2, type="l")

![](Post_Live_Session_9_files/figure-markdown_strict/unnamed-chunk-4-1.png)<!-- -->

Here I add 200 to the 55 visitor value to see if there is much change.
The cyclical plot do no change but the outlier is apparent in the season
adjusted data.

    fitd2 <- decompose(visitors2)
    seasonadj2 <- seasadj(fitd2)
    plot(seasonadj2, type="l")

![](Post_Live_Session_9_files/figure-markdown_strict/unnamed-chunk-5-1.png)<!-- -->

F. Does it make any difference if the outlier is near the end rather
than in the middle of the time series?

No in class we ran multiple different simulation and for each the
outlier was still prominent regardless of position.

G.Use STL to decompose the series

    fitvisitors <- stl(visitors, s.window = 5)
    plot(fitvisitors)

![](Post_Live_Session_9_files/figure-markdown_strict/unnamed-chunk-6-1.png)<!-- -->

    plot(visitors)
    lines(fitvisitors$time.series[,2],col="red",ylad="Trend")

    ## Warning in plot.xy(xy.coords(x, y), type = type, ...): "ylad" is not a
    ## graphical parameter

![](Post_Live_Session_9_files/figure-markdown_strict/unnamed-chunk-6-2.png)<!-- -->

Part 2
------

We were each assigned stocks and asked to calculated the log return and
measure volatility. I got PSXP, which stands for Phillips 66 Partners,
which is an Oil company

First we grab the data from yahoo

    library(tseries)
    PEPdata <- get.hist.quote('psxp',quote="Close")

    ## time series starts 2013-07-23

    length(PEPdata)

    ## [1] 753

Show the length of the dataset

Now we take the log of the dataset to get the log returns

    PEPret <- log(lag(PEPdata))-log(PEPdata)
    PEPvol <- sd(PEPret) * sqrt(250) * 100
    PEPvol 

    ## [1] 37.34803

And now we calculate the non continuous volatility shown above. Did not
print out the table because it was very long.

Below is a loop that creates a continuous lookback windown for the
volatility measurement

    PEPVol <- function(d, logrets){ 
      var=0 
      lam= 0 
      varlist <- c() 
      for(r in logrets){
        lam = lam*(1 - 1/d) + 1
        var = (1-1/lam)*var + (1/lam)*r^2
        varlist <- c(varlist,var)} 
      sqrt(varlist)}

Weight as 10% 30% and 100%

    PEPvolest <- PEPVol(10,PEPret)
    PEPvolest2 <- PEPVol(30,PEPret)
    PEPvolest3 <- PEPVol(100,PEPret)

    plot(PEPvolest, type="l")
    lines(PEPvolest2, type="l", col="red")
    lines(PEPvolest3, type="l", col="blue")

![](Post_Live_Session_9_files/figure-markdown_strict/unnamed-chunk-10-1.png)<!-- -->
Plotted all together

    plot(PEPvolest, type="l")
    lines(PEPvolest2, type="l", col="red")
    lines(PEPvolest3, type="l", col="blue")

![](Post_Live_Session_9_files/figure-markdown_strict/unnamed-chunk-11-1.png)<!-- -->

Note that the `echo = FALSE` parameter was added to the code chunk to
prevent printing of the R code that generated the plot.
