# Reproducible Research: Peer Assessment 1



## Loading and preprocessing the data
### 1. Load the data (i.e. read.csv())
##### => Read the file and the packages that are needed.

```r
library(ggplot2)
library(scales)
library(Hmisc)
```

```
## Warning: package 'Hmisc' was built under R version 3.3.1
```

```
## Loading required package: lattice
```

```
## Loading required package: survival
```

```
## Loading required package: Formula
```

```
## 
## Attaching package: 'Hmisc'
```

```
## The following objects are masked from 'package:base':
## 
##     format.pval, round.POSIXt, trunc.POSIXt, units
```

```r
actdata <- read.csv("activity.csv")
```
### 2. Process/transform the data (if necessary) into a format suitable for your analysis
##### => Transform the "date" column into the type of "Date"

```r
actdata$date <- as.Date(actdata$date)
```

-----

## What is mean total number of steps taken per day?
    
    ```r
    stepsperday <- tapply(actdata$steps, actdata$date, sum, na.rm=TRUE)
    ```

### 1. Make a histogram of the total number of steps taken each day

```r
qplot(stepsperday, xlab='Total Steps Per Day')
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](https://github.com/LuoGuoFong/RepData_PeerAssessment1/blob/master/figure/unnamed-chunk-5-1.png)<!-- -->

### 2. Calculate and report the mean and median total number of steps taken per day

```r
stepsperdaymean <- mean(stepsperday)
stepsperdaymedian <- median(stepsperday)
```
* Mean: 9354.2295082
* Median:  10395

-----
    
## What is the average daily activity pattern?
    
    ```r
    avgsteps <- aggregate(x=list(avgsteps=actdata$steps), by=list(interval=actdata$interval), FUN=mean, na.rm=TRUE)
    ```

### 1. Make a time series plot

```r
ggplot(data=avgsteps, aes(x=interval, y=avgsteps)) +
    geom_line() +
    xlab("5-Minute Interval") +
    ylab("Average Number of Steps Taken") 
```

![](RepData_PeerAssessment1/figure/unnamed-chunk-8-1.png)<!-- -->

### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
maxinterval <- avgsteps$interval[which(avgsteps$avgsteps == max(avgsteps$avgsteps))]
```

* Most Steps at: 835

----
    
## Imputing missing values
### 1. Calculate and report the total number of missing values in the dataset 
    
    ```r
    NumMissing <- length(which(is.na(actdata$steps)))
    ```

* Number of missing values: 2304

### 2. Devise a strategy for filling in all of the missing values in the dataset.
### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
actfill <- actdata
actfill$steps <- impute(actdata$steps, fun=mean)
```


### 4. Make a histogram of the total number of steps taken each day 

```r
stepsbydayfill <- tapply(actfill$steps, actfill$date, sum)
qplot(stepsbydayfill, xlab='Total steps per day (Imputed)', ylab='Frequency using binwith 500', binwidth=500)
```

![](RepData_PeerAssessment1/figure/unnamed-chunk-12-1.png)<!-- -->

### ... and Calculate and report the mean and median total number of steps taken per day. 

```r
stepsbydayfillmean <- mean(stepsbydayfill)
stepsbydayfillmedian <- median(stepsbydayfill)
```
* Mean (Imputed): 1.0766189\times 10^{4}
* Median (Imputed):  1.0766189\times 10^{4}


----
    
## Are there differences in activity patterns between weekdays and weekends?
##### 1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
    
    
    ```r
    actfill$dateType <-  ifelse(as.POSIXlt(actfill$date)$wday %in% c(0,6), 'weekend', 'weekday')
    ```

### 2. Make a panel plot containing a time series plot


```r
avgactfill <- aggregate(steps ~ interval + dateType, data=actfill, mean)
ggplot(avgactfill, aes(interval, steps)) + 
    geom_line() + 
    facet_grid(dateType ~ .) +
    xlab("5-minute interval") + 
    ylab("avarage number of steps")
```

![](RepData_PeerAssessment1/figure/unnamed-chunck-15-1.png)<!-- -->
