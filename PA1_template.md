---
title: "PA_Template"
author: "Dawn"
date: "Wednesday, February 11, 2015"
output: html_document
---


## Loading and preprocessing the data

```r
## Set working directory

setwd("c:/coursera/")

## Read Source Data

actdata <- read.csv('activity.csv')
```


## What is mean total number of steps taken per day?

```r
## Calculate total steps per day without 
## regard to rows with NA

totalspd <- tapply(actdata$steps, actdata$date, sum)
```

```r
## Histogram of total steps taken each day

hist(totalspd, breaks = 5, main = "Number of Steps Taken Per Day", 
     xlab = " # of Steps", ylab = "# of Days in Group", col = "Firebrick")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

```r
## Calculate Mean Steps per Day

meanspd <- mean(totalspd, na.rm = T)
meanspd
```

```
## [1] 10766.19
```

```r
## Calculate Median Steps per day

medianspd <- median(totalspd, na.rm = T)
medianspd
```

```
## [1] 10765
```



## What is the average daily activity pattern?


```r
## Time series plot (type = "l") of the 5-minute interval (x-axis) 
## and the average number of steps taken, averaged across all days (y-axis)

meanspi <- tapply(actdata$steps, actdata$interval, mean, na.rm = T)
plot(meanspi, type="l", main =  "Average Steps Per Interval", ylab = "# of Steps")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 


```r
## Find interval with maximum number of steps

seq(along = meanspi)[meanspi == max(meanspi)]
```

```
## [1] 104
```

## Imputing missing values

```r
## Calculate number of missing values

sum(as.numeric(is.na(actdata$steps)))
```

```
## [1] 2304
```

```r
## fill in missing data

##Set mean steps interval data as a vector and repeat it for the 61 days
fillmeanstepint <- as.vector(meanspi)
fillmeanstepint <- rep(fillmeanstepint, 61)

## set value to 1 where it is not NA
fillmeanstepint [!is.na(actdata$steps)] = 1

## steps data as a vector and set to where data is missing
fillstepdata <- as.vector(actdata$steps)
fillstepdata [is.na(fillstepdata)] = 1


cleandata <- actdata
cleandata$steps <- fillmeanstepint * fillstepdata

tspdnomiss<-tapply(cleandata$steps,cleandata$date,sum)
```

```r
## Create histogram
hist(tspdnomiss, breaks = 5, main = "Number of Steps Taken Per Day", 
     xlab = " # of Steps", ylab = "# of Days in Group", col = "Firebrick")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png) 

Imputing the values for missing data increases the average number of steps taken per day as can be seen by comparing the middle bar of the histograms for Number of Steps taken per day in this chart to the histogram 2 charts above.

## Are there differences in activity patterns between weekdays and weekends?

```r
## set up weekdays and weekends

days1 <- as.POSIXlt(cleandata$date, format = "%Y-%m-%d")
days2 <- days1$wday
days2[days2 == 0] = 0
days2[days2 == 6] = 0
days2[days2 != 0] = 1
days3 <- factor(days2, levels = c(0,1))
cleandata$WD <- days3

wdmeansteps <- tapply(cleandata$steps, list(cleandata$interval, cleandata$WD), mean, na.rm = T)
```

```r
## plot weekday over weekend time series
par(mfrow = c(2,1))

with(cleandata, {
##  par(maj = c(1, 1, 1, 1))
  plot(wdmeansteps[,1],type = "l", col = "Tomato", main = ("Steps In Each Interval"),
       xlab = "Interval", ylab = "Weekend")
  title = ("# of Steps per Interval")
##  par(maj = c(1, 1, 1 , 1)
  plot(wdmeansteps[, 2], type = "l", col = "Blue", 
       xlab="Interval", ylab = "Weekdays")
})
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png) 
