# Reproducible Research: Peer Assessment 1
Ken Lee  
August 25, 2016  

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
### 3. Draw histogram of steps per day using ggplot

```r
ggplot(rw_ag_steps, aes(x = steps)) +
    geom_histogram(fill = "blue", binwidth = 1000) +
    labs(title = "Histogram of Steps per day", 
         x = "Steps per day", 
         y = "Frequency")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

### 4. Calculate mean and median of the aggregated steps

```r
mean_steps <- mean(rw_ag_steps$steps, na.rm = TRUE)
median_steps <- median(rw_ag_steps$steps, na.rm = TRUE)
print(mean_steps)
```

[1] 10766.19

```r
print(median_steps)
```

[1] 10765

## What is the average daily activity pattern?



## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
