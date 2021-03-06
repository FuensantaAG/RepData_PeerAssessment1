# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

1) Load the data


```r
data<-read.csv("activity.csv")
```

2) Process/transform the data (if necessary) into a format suitable for your analysis



```r
TotalStepsPerDay<-aggregate(steps~date,data=data,sum,na.rm=TRUE)
AverageStepsPerInterval<-aggregate(steps~interval,data=data,mean,na.rm=TRUE)
```

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1) Make a histogram of the total number of steps taken each day


```r
hist(TotalStepsPerDay$steps, main="Histogram", xlab="Total steps per day")
```

![](./PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

2) Calculate and report the mean and median total number of steps taken per day


```r
mean(TotalStepsPerDay$steps)
```

```
## [1] 10766.19
```

```r
median(TotalStepsPerDay$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1) Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
plot(AverageStepsPerInterval$interval,AverageStepsPerInterval$steps,type="l",xlab="Day Interval",ylab="Average number of steps",main="Average number of steps by Day Interval")
```

![](./PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

2) Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?



```r
interval.max<-AverageStepsPerInterval[which.max(AverageStepsPerInterval$steps),]$interval
```

The 5-minute interval, on average across all the days in the dataset, which contains the maximum number of steps is 835.

## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1) Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
non_complete<-nrow(data)-sum(complete.cases(data))
```

The number of non-complete cases (with NAs) is 2304.

2) Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

The strategy adopted is filling all the NAs with the average number of steps in the file.


```r
impute.mean <- function(x) {
  z <- mean(data$steps, na.rm = TRUE)
  x[is.na(x)] <- z
  return(x)
}
data2 <- sapply(data, function(x){
  if(is.numeric(x) & any(is.na(x))){
    impute.mean(x)
  } else {
    x
  }
}
)
```

3) Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
data2<-data.frame(data2)
data2$date<-data$date
```

4) Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
TotalStepsPerDay2<-aggregate(steps~date,data=data2,sum,na.rm=TRUE)
AverageStepsPerInterval2<-aggregate(steps~interval,data=data2,mean,na.rm=TRUE)
```

4.1) Make a histogram of the total number of steps taken each day


```r
hist(TotalStepsPerDay2$steps, main="Histogram", xlab="Total steps per day")
```

![](./PA1_template_files/figure-html/unnamed-chunk-11-1.png) 

4.2) Calculate and report the mean and median total number of steps taken per day


```r
mean(TotalStepsPerDay2$steps)
```

```
## [1] 10766.19
```

```r
median(TotalStepsPerDay2$steps)
```

```
## [1] 10766.19
```

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1) Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
data2$day <- weekdays(as.Date(data2$date))
data2$weekday <- ifelse(data2$day=="sabado"|data2$day=="domingo",c("weekend"), c("weekday")) 
```

2) Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).



```r
library(lattice)
AverageStepsPerIntervalDay<-aggregate(steps~interval+weekday,data=data2,mean,na.rm=TRUE)
xyplot(steps~interval|weekday,data=AverageStepsPerIntervalDay,type="l",layout=c(1,2))
```

![](./PA1_template_files/figure-html/unnamed-chunk-13-1.png) 


