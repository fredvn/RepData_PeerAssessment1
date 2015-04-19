# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

Create a directory for the created figures
Create directory for the downloaded file if it did not exists
Download zipfile and unzip it
load het dataset
Default language to english

```r
Sys.setenv(LANG = "en")
setInternet2(TRUE)

if(!file.exists("figure")){
  dir.create("figure");
}

if(!file.exists("data")){
  dir.create("data");
}

fileUrl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
if (!file.exists("data/activity_monitoring_data.zip")) {
  download.file(fileUrl, destfile = "./data/activity_monitoring_data.zip")
}

if (!file.exists("./data/activity.csv")) {
  unzip("./data/activity_monitoring_data.zip",exdir="./data")
}

data <- read.csv("./data/activity.csv")
```

## What is mean total number of steps taken per day?

aggregate the steps for each day and the frequency for the total steps per day in a histogram

```r
StepsPerDay <- aggregate(steps ~ date, data=data,  FUN=sum, na.rm=TRUE)
png(filename="figure/stepsPerDay.png")
histogram <- hist(StepsPerDay$steps)
dev.off()
```

```
## png 
##   2
```

```r
histogram <- hist(StepsPerDay$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 


```r
maxSteps <- max(StepsPerDay$steps)
meanSteps <- mean(StepsPerDay$steps)
```

The maximum number of steps in a day is 21194
The mean number of steps in a day is 1.0766189\times 10^{4}

## What is the average daily activity pattern?

This figure shows the average steps per 5 minute interval.

```r
StepsPerInterval <- aggregate(steps ~ interval, data=data, FUN=mean, na.rm=TRUE)
png(filename="figure/stepsPerInterval.png")
plot <- plot(StepsPerInterval$interval,StepsPerInterval$steps,type="l")
dev.off()
```

```
## png 
##   2
```

```r
plot <- plot(StepsPerInterval$interval,StepsPerInterval$steps,type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

## Imputing missing values



The total number of missing values is 2304

create dataset where missing values are replaced bij the avarage of the 5 minute interval

```r
filled <- data
```


```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
## 
## The following object is masked from 'package:stats':
## 
##     filter
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
StepsPerDayFilled <- aggregate(steps ~ date, data=filled,  FUN=sum, na.rm=TRUE)
png(filename="figure/stepsPerDayFilled.png")
histogram <- hist(StepsPerDayFilled$steps)
dev.off()
```

```
## png 
##   2
```

```r
histogram <- hist(StepsPerDayFilled$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 



The maximum number of steps per day replacing the not available values with the avarage of the dataset for the interval is 21194
The avarege number of steps per day replacing the not available values with the avarage of the dataset for the interval is 1.0766189\times 10^{4}

## Are there differences in activity patterns between weekdays and weekends?

```r
library(ggplot2)
library(plyr)
```

```
## -------------------------------------------------------------------------
## You have loaded plyr after dplyr - this is likely to cause problems.
## If you need functions from both plyr and dplyr, please load plyr first, then dplyr:
## library(plyr); library(dplyr)
## -------------------------------------------------------------------------
## 
## Attaching package: 'plyr'
## 
## The following objects are masked from 'package:dplyr':
## 
##     arrange, count, desc, failwith, id, mutate, rename, summarise,
##     summarize
```

```r
Sys.setlocale("LC_TIME", "English")
```

```
## [1] "English_United States.1252"
```

```r
weekOrWeekend <-  mutate(data,group = ifelse (weekdays(as.Date(data$date),TRUE) %in% c("Sat ","Sun"),"weekend","weekday"))
aggregated <- ddply(weekOrWeekend,c("group","interval"),summarize,steps=mean(steps,na.rm=TRUE))

img <- qplot(interval,steps
             ,data=aggregated
            ,facets=(group~.)
            ,geom=c("line")
            ,main="Avarage number of steps in a 5 minute interval for weekdays and weekend"
            ,xlab="Interval"
            ,ylab="Number of steps"
            )
png(filename="figure/avgStepsInWeekOrWeekend.png")
print(img)
dev.off()
```

```
## png 
##   2
```

```r
print(img)
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 
