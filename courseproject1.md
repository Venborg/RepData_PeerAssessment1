Loading the data
================

First the activity data will be loaded

    data <- read.csv("/Users/Thansen/Desktop/R/ReproducibleResearch/activity.csv")
    data <- na.omit(data)

Processing data
===============

Here I find the sum of the steps taken each day. I start by looking at
the data. Then I create a new datatable, where the steps are summed up
by date, then I add the columnnames. Finally I print the new steps
table, where its now clear how many steps where taken each of the dates
where ther is a step.

    head(data)

    ##     steps       date interval
    ## 289     0 2012-10-02        0
    ## 290     0 2012-10-02        5
    ## 291     0 2012-10-02       10
    ## 292     0 2012-10-02       15
    ## 293     0 2012-10-02       20
    ## 294     0 2012-10-02       25

    library(data.table)
    stepstabel <-  as.data.table(data)[, by = . (date), sum(steps)]
    colnames(stepstabel) <- c("Date","steps")
    stepstabel$Date <- format(as.Date(stepstabel$Date), "%Y/%m/%d")
    print(stepstabel)

    ##           Date steps
    ##  1: 2012/10/02   126
    ##  2: 2012/10/03 11352
    ##  3: 2012/10/04 12116
    ##  4: 2012/10/05 13294
    ##  5: 2012/10/06 15420
    ##  6: 2012/10/07 11015
    ##  7: 2012/10/09 12811
    ##  8: 2012/10/10  9900
    ##  9: 2012/10/11 10304
    ## 10: 2012/10/12 17382
    ## 11: 2012/10/13 12426
    ## 12: 2012/10/14 15098
    ## 13: 2012/10/15 10139
    ## 14: 2012/10/16 15084
    ## 15: 2012/10/17 13452
    ## 16: 2012/10/18 10056
    ## 17: 2012/10/19 11829
    ## 18: 2012/10/20 10395
    ## 19: 2012/10/21  8821
    ## 20: 2012/10/22 13460
    ## 21: 2012/10/23  8918
    ## 22: 2012/10/24  8355
    ## 23: 2012/10/25  2492
    ## 24: 2012/10/26  6778
    ## 25: 2012/10/27 10119
    ## 26: 2012/10/28 11458
    ## 27: 2012/10/29  5018
    ## 28: 2012/10/30  9819
    ## 29: 2012/10/31 15414
    ## 30: 2012/11/02 10600
    ## 31: 2012/11/03 10571
    ## 32: 2012/11/05 10439
    ## 33: 2012/11/06  8334
    ## 34: 2012/11/07 12883
    ## 35: 2012/11/08  3219
    ## 36: 2012/11/11 12608
    ## 37: 2012/11/12 10765
    ## 38: 2012/11/13  7336
    ## 39: 2012/11/15    41
    ## 40: 2012/11/16  5441
    ## 41: 2012/11/17 14339
    ## 42: 2012/11/18 15110
    ## 43: 2012/11/19  8841
    ## 44: 2012/11/20  4472
    ## 45: 2012/11/21 12787
    ## 46: 2012/11/22 20427
    ## 47: 2012/11/23 21194
    ## 48: 2012/11/24 14478
    ## 49: 2012/11/25 11834
    ## 50: 2012/11/26 11162
    ## 51: 2012/11/27 13646
    ## 52: 2012/11/28 10183
    ## 53: 2012/11/29  7047
    ##           Date steps

Creating a histogram
====================

    steps1 <- hist(stepstabel$steps, breaks =20, main = "Total steps per day", xlab ="Steps", ylab =  "Frequency", col ="green")

![](courseproject1_files/figure-markdown_strict/unnamed-chunk-2-1.png)

Plotting the daily activity pattern
===================================

To create the plot, i first stort the data in a sum of step by the
intervals. To get the line plot, I type = "l". If this isn't specified,
the plot will be a scatterplot.

    interval <- as.data.table(data)[,sum(steps), by = .(interval)] 
    colnames(interval) <- c("interval", "steps")
    plot(interval, type = "l")

![](courseproject1_files/figure-markdown_strict/unnamed-chunk-3-1.png)

Finding the interval with the maximum numbers of steps is pretty
straight forward.

    maxstep <- sapply (interval, max)
    interval[steps %in% maxstep,]

    ##    interval steps
    ## 1:      835 10927

This means that interval 835 has the largest amount of steps.

Imputing missing values
=======================

Since I removed the NA's from the first dataset, I reload it and in a
new data name. Calculating the total amount of missing values from the
original dataset

    orgdata <- read.csv("/Users/Thansen/Desktop/R/ReproducibleResearch/activity.csv")
    sum(is.na(orgdata))

    ## [1] 2304

    nrow(orgdata)

    ## [1] 17568

    sum(is.na(orgdata))/nrow(orgdata)

    ## [1] 0.1311475

There was 2304 missing values. That seems like quite a lot, actually it
is 13%.

To fill in the missing values, I take the mean of the filled-in days.

    orgdata$steps[is.na(orgdata$steps)] <-  mean(data$steps, na.rm = FALSE)
    head(orgdata)

    ##     steps       date interval
    ## 1 37.3826 2012-10-01        0
    ## 2 37.3826 2012-10-01        5
    ## 3 37.3826 2012-10-01       10
    ## 4 37.3826 2012-10-01       15
    ## 5 37.3826 2012-10-01       20
    ## 6 37.3826 2012-10-01       25

    sum(is.na(orgdata))

    ## [1] 0

There is no longer any missing values in the data.

    filled_in_data <-  as.data.table(orgdata)[, by = . (date), sum(steps)]
    colnames(filled_in_data) <- c("interval", "steps")
    steps2 <- hist(filled_in_data$steps, breaks = 20, xlab = "Steps", main = "The total amount of steps with filled in data", col ="red")

![](courseproject1_files/figure-markdown_strict/unnamed-chunk-7-1.png)

Combining the two histograms to see the difference

    set.seed(42)
    plot(steps1, col = rgb(0,0,1, 1/4), xlim = c(0,24000))
    plot(steps2, col = rgb(1,0,0,1/4), xlim = c(0,24000), add=T)

![](courseproject1_files/figure-markdown_strict/unnamed-chunk-8-1.png)

Weekdays vs weekends
====================

First I'm adding and extra row where the weekend, weekday distinguishion
is made.

    orgdata$realdate <-  as.Date(orgdata$date, format = "%Y-%m-%d")
    orgdata$weekday <- weekdays(orgdata$realdate)

    orgdata$daytype <- ifelse(orgdata$weekday == "Saturday" | orgdata$weekday == "Sunday", "weekend", "weekday")
    head(orgdata, n = 10) 

    ##      steps       date interval   realdate weekday daytype
    ## 1  37.3826 2012-10-01        0 2012-10-01  Monday weekday
    ## 2  37.3826 2012-10-01        5 2012-10-01  Monday weekday
    ## 3  37.3826 2012-10-01       10 2012-10-01  Monday weekday
    ## 4  37.3826 2012-10-01       15 2012-10-01  Monday weekday
    ## 5  37.3826 2012-10-01       20 2012-10-01  Monday weekday
    ## 6  37.3826 2012-10-01       25 2012-10-01  Monday weekday
    ## 7  37.3826 2012-10-01       30 2012-10-01  Monday weekday
    ## 8  37.3826 2012-10-01       35 2012-10-01  Monday weekday
    ## 9  37.3826 2012-10-01       40 2012-10-01  Monday weekday
    ## 10 37.3826 2012-10-01       45 2012-10-01  Monday weekday

Plotting the weekdays vs. weekends

    sumdata_interval <- aggregate(steps~interval+daytype,data = orgdata, FUN = mean, na.action = na.omit)
    head(sumdata_interval)

    ##   interval daytype    steps
    ## 1        0 weekday 7.006569
    ## 2        5 weekday 5.384347
    ## 3       10 weekday 5.139902
    ## 4       15 weekday 5.162124
    ## 5       20 weekday 5.073235
    ## 6       25 weekday 6.295458

    library(lattice)
    xyplot(steps ~interval | daytype, data= sumdata_interval, layout =c(1,2), type = "l")

![](courseproject1_files/figure-markdown_strict/unnamed-chunk-10-1.png)
