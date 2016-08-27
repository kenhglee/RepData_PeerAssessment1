# Reproducible Research: Peer Assessment 1
Ken Lee  
August 27, 2016  

## Loading Libraries

```r
library(lubridate)
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following object is masked from 'package:base':
## 
##     date
```

```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:lubridate':
## 
##     intersect, setdiff, union
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(ggplot2)
```

## Loading and preprocessing the data
### 1. Load the raw data

```r
rw <- read.csv("activity.csv", header = TRUE, 
               colClasses = c("numeric", "character","integer"))
```
### 2. Process the data

```r
rw$date <- ymd(rw$date)
```

## What is mean total number of steps taken per day?
### 1. Filter out null steps

```r
rw_ft = filter(rw, !is.na(steps))
```
### 2. Aggregate sum of steps per day

```r
rw_ag_steps = aggregate(steps ~ date, data = rw_ft, sum)
```
### 3. Histogram of the total number of steps taken each day

```r
ggplot(rw_ag_steps, aes(x = steps)) +
    geom_histogram(fill = "blue", binwidth = 1000) +
    labs(title = "Histogram of Steps per day", 
         x = "Steps per day", 
         y = "Frequency")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png)<!-- -->
![histogram 1](instructions_fig/unnamed-chunk-6-1.png) 

### 4. Mean and median number of steps taken each day

```r
mean(rw_ag_steps$steps, na.rm = TRUE)
```

[1] 10766.19

```r
median(rw_ag_steps$steps, na.rm = TRUE)
```

[1] 10765

## What is the average daily activity pattern?
### 1. Aggregate mean of steps per interval

```r
rw_ag_act = aggregate(steps ~ interval, data = rw_ft, mean)
```

### 2. Time series plot of the average number of steps taken

```r
ggplot(rw_ag_act, aes(x=interval, y=steps)) +
    geom_line(color = "blue")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
![time series plot](instructions_fig/unnamed-chunk-9-1.png)     

### 3. The 5-minute interval that, on average, contains the maximum number of steps

```r
rw_ag_act[which.max(rw_ag_act$steps),]
```

    interval    steps
104      835 206.1698

## Imputing missing values
### 1. Calculate and report the total number of missing values in the dataset 

```r
sum(is.na(rw$steps))
```

[1] 2304

### 2. Create a new dataset that is equal to the original dataset but with the missing data filled in
#### The idea is to 
1. Extract an array of boolean index: <span style="color:red">na_index</span>
2. Calculate average steps per 5 min interval using <span style="color:red">tapply</span>
3. Fill in each NA values by using the average steps per interval

```r
rw_full <- rw
na_index <- is.na(rw_full$steps)
avg_interval <- tapply(rw_full$steps, rw_full$interval, mean, na.rm=TRUE, simplify=TRUE)
rw_full$steps[na_index] <- avg_interval[as.character(rw_full$interval[na_index])]

sum(is.na(rw_full$steps))
```

```
## [1] 0
```

### 3. Aggregate sum of steps per date

```r
steps_full = filter(rw_full, !is.na(steps))
steps_ag_full = aggregate(steps ~ date, data=steps_full, sum)
```

### 4. Make a histogram of the total number of steps taken each day

```r
ggplot(steps_ag_full, aes(x = steps)) +
    geom_histogram(fill = "blue", binwidth = 1000) +
    labs(title = "Histogram of Steps per day, including missing values", 
         x = "Steps per day", y = "Frequency")
```

![](PA1_template_files/figure-html/unnamed-chunk-14-1.png)<!-- -->
![histogram 2](instructions_fig/unnamed-chunk-14-1.png)

### 5.Calculate and report the mean and median total number of steps taken per day

```r
mean(steps_ag_full$steps, na.rm = TRUE)
```

[1] 10766.19

```r
median(steps_ag_full$steps, na.rm = TRUE)
```

[1] 10766.19

#### Note: The impact of the imputting turns out eqaulizing mean steps to median.

## Are there differences in activity patterns between weekdays and weekends?
### 1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" 

```r
rw_full <- mutate(rw_full, weektype = ifelse(weekdays(rw_full$date) == "Saturday" 
                                             | weekdays(rw_full$date) == "Sunday", 
                                             "weekend", "weekday"))
rw_full$weektype <- as.factor(rw_full$weektype)
```

### 2. Aggregate mean of steps per interval and weektype 

```r
interval_full = aggregate(steps ~ interval + weektype, data=rw_full, mean)
```

### 3. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 

```r
ggplot(interval_full, aes(x=interval, y=steps, color = weektype)) +
    geom_line() + facet_wrap(~weektype, ncol = 1, nrow=2)
```

![](PA1_template_files/figure-html/unnamed-chunk-18-1.png)<!-- -->
![panel plot](instructions_fig/unnamed-chunk-18-1.png)
