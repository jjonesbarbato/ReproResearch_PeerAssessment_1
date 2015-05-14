---
title: "Reproducible Research - Peer Assessment 1"
author: "Jenifer Jones"
date: "Saturday, April 18, 2015"
output: html_document
---

<h1><b>Introduction</b></h1>

This document supports the work required for Peer Assessment 1 of the 
Reproducible Research course offered through Coursera.  This assignment uses R
Markdown and knitr to transform text and R code into an HTML file.  

The data used in this assessment consists of information from about personal 
movement using activity monitoring devices.  The devices collect data at five 
minute intervals throughout the day.  The data consists of two months of data 
collected during the months of October and November 2012 from an anonymized 
individual. The data includes the number of steps taken during those five 
minute intervals. 

<h2><b>Data</b></h2>
The dataset is stored in a comma-seperated value (CSV) file and consists 
of 17,568 observations.  

The variables included are: 
  - <b>steps:</b> Number of steps taken in a 5-minute interval (missing values 
  coded as "NA")
  - <b>date:</b> The date on which the measurement was taken in YYYY-MM-DD 
  format
  - <b>interval:</b> Identifier for the 5-minute interval in which measurement 
  was taken 
  
<h2><b>Assignment details</b></h2>

There are multiple parts of the assignment including creating a single R 
markdown document that can be processed by knitr to create an HTML file.  
Throughout this report the output will be shown and the command echo=TRUE used
so that others can read the code output.  For plotting the library ggplot2 will
be used.  

The github repository related to this assignment can be found at: 
(https://github.com/jjonesbarbato/RepData_PeerAssessment1)

<h1><b>Assignment Output</b></h1>

<h2><b>Loading and preprocessing the data</b></h2>

Load R libraries: 

```r
library(knitr)
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.1.2
```

```r
library(plyr)
library (dplyr)
```

```
## Warning: package 'dplyr' was built under R version 3.1.2
```

```
## 
## Attaching package: 'dplyr'
## 
## The following objects are masked from 'package:plyr':
## 
##     arrange, count, desc, failwith, id, mutate, rename, summarise,
##     summarize
## 
## The following object is masked from 'package:stats':
## 
##     filter
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```
Set the global options for knitr so that we also display the r code: 

```r
opts_chunk$set(echo=TRUE)
```
<b>NB:</b> For the following steps it is assumed that the file is in your working 
directory. 

1. Load the data


```r
activity_data<-read.csv("activity.csv", header=TRUE)
```
2. Process/transform the data (if necessary) into a format suitable for 
analysis  
  Running the following to show the structure of the file shows that the date 
  is a factor and the interval field is an interger.  
  

```r
str(activity_data)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```
  
  Based on this two transformations will be done, convert the date field to 
  <b>DATE</b> class and the interval field to <b>FACTOR</b>.
  

```r
activity_data$date<-as.Date(activity_data$date)
activity_data$interval<-as.factor(activity_data$interval)
```
  Re-running the str() command shows us that our changes to the columns have
  been made. 

```r
str(activity_data)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Date, format: "2012-10-01" "2012-10-01" ...
##  $ interval: Factor w/ 288 levels "0","5","10","15",..: 1 2 3 4 5 6 7 8 9 10 ...
```


<h2><b>What is the mean total number of steps taken per day?</b></h2>
NB: During this part the missing values (NA) will be ignored  

1. Calculate the total number of steps taken per day


```r
total_steps_perday<-activity_data %>% group_by(date) %>% summarize(sum(steps, na.rm=TRUE))
#Tidy dataset by assigning appropriate column names
colnames(total_steps_perday)<-c("Date", "Total_Steps")
#View first ten rows of the dataset
head(total_steps_perday,10)
```

```
## Source: local data frame [10 x 2]
## 
##          Date Total_Steps
## 1  2012-10-01           0
## 2  2012-10-02         126
## 3  2012-10-03       11352
## 4  2012-10-04       12116
## 5  2012-10-05       13294
## 6  2012-10-06       15420
## 7  2012-10-07       11015
## 8  2012-10-08           0
## 9  2012-10-09       12811
## 10 2012-10-10        9900
```
2. Make a histogram of the total number of steps taken each day  

```r
hist(total_steps_perday$Total_Steps, 
     main = "Histogram of Total Steps per Day", xlab = "Total Steps per Day", 
     col= "blue")  
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png) 

3. Calculate and report the mean and median of the total number of steps taken
per day


```r
mean(total_steps_perday$Total_Steps, na.rm=TRUE)
```

```
## [1] 9354.23
```

```r
median(total_steps_perday$Total_Steps, na.rm=TRUE)
```

```
## [1] 10395
```

The output tells us that the mean is <b>9354.23</b> steps and the median is 
<b> 10395 </b> steps.  


<h2><b>What is the average daily pattern?</b></h2>  

1. Create a time series plot of the 5-minute interval (x-axis) and the average
number of steps taken, averaged actoss all days (y-axis)  

First we need to summarize the data to show us average number of steps per 
interval.  


```r
avg_steps_perint<-activity_data %>% group_by(interval)%>% 
  summarize(mean(steps, na.rm=TRUE))
#Tidy dataset by assigning appropriate column names
colnames(avg_steps_perint)<-c("interval", "Avg_Steps")
#View first ten rows of the dataset
head(avg_steps_perint,10)
```

```
## Source: local data frame [10 x 2]
## 
##    interval Avg_Steps
## 1         0 1.7169811
## 2         5 0.3396226
## 3        10 0.1320755
## 4        15 0.1509434
## 5        20 0.0754717
## 6        25 2.0943396
## 7        30 0.5283019
## 8        35 0.8679245
## 9        40 0.0000000
## 10       45 1.4716981
```

Now we will create a time series plot using the data set with the averages.

<b>NB:</b> After trying to plot a few times and receiving an error the 
Interval value was changed from <b>factor</b> to <b>numeric</b>.  

```r
avg_steps_perint$interval<-as.numeric(levels(avg_steps_perint$interval))[avg_steps_perint$interval]
```

```r
ggplot(data=avg_steps_perint, 
       aes(x = interval, y = Avg_Steps)) + 
       geom_line(color="blue") + xlab("Interval") + ylab("Step Average") + 
       ggtitle("Average number of steps per interval")  
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, 
contains the maximum number of steps? 

Based on looking at the graph we would expect the value to be around 800. 

Proving it out with R.


```r
avg_steps_perint[which.max(avg_steps_perint$Avg_Steps), ]
```

```
## Source: local data frame [1 x 2]
## 
##   interval Avg_Steps
## 1      835  206.1698
```

From this we see that the interval with the maximum average number of steps is
<b>835</b> and the corresponding average is <b>206.17 steps</b>.


<h2><b>Imputing missing values</b></h2>

As noted earlier there are days/intervals with missing values.  This may
introduce bias into calculations and summaries of the data. 

1. Calculate and report the total number of missing values in the dataset

This can be done using the is.na function along with sum to provide the total. 


```r
sum(is.na(activity_data$steps))
```

```
## [1] 2304
```

From this output we see that there are <b> 2304 </b> missing values. 

2. Devise a strategy for filling in all of the missing values in the dataset.  

We can create a new dataset by joining the original activity data with the
average by interval.  We can then take those rows where the steps = "NA" and
replace it with the value we have included in the "Avg_Steps" column. 

<b>NB:</b> We need to update the original data set interval column so that
is it numeric.  

3. Create a new dataset that is equal to the original but with the missing
data filled in. 



```r
activity_data$interval<-as.numeric(levels(activity_data$interval))[activity_data$interval]
merged_activity<-left_join(activity_data, avg_steps_perint, by = "interval")
#Create a new column which has the result of the logical check for NA. 
#The Average will be used if it is true and the original steps value if it isn't.
merged_activity$updated_steps<-ifelse(is.na(merged_activity$steps), merged_activity$Avg_Steps, merged_activity$steps)
```

4. Make a histogram of the total number of steps taken each day and calculate
and report the <b>mean</b> and <b>median</b> total number of steps taken per day. 
Do these differ from the estimates from the first part of the assignment? What
is the impact of imputing the missing data on the estimates of the total daily 
number of steps?

First create a summarized data set grouped by date with the sum of the updated steps 
value. 


```r
upd_total_steps_perday<-merged_activity %>% group_by(date) %>% summarize(sum(updated_steps))
#Tidy dataset by assigning appropriate column names
colnames(upd_total_steps_perday)<-c("Date", "Total_Steps_Updated")
#View first ten rows of the dataset
head(upd_total_steps_perday,10)
```

```
## Source: local data frame [10 x 2]
## 
##          Date Total_Steps_Updated
## 1  2012-10-01            10766.19
## 2  2012-10-02              126.00
## 3  2012-10-03            11352.00
## 4  2012-10-04            12116.00
## 5  2012-10-05            13294.00
## 6  2012-10-06            15420.00
## 7  2012-10-07            11015.00
## 8  2012-10-08            10766.19
## 9  2012-10-09            12811.00
## 10 2012-10-10             9900.00
```

Using the similar code as before create another histogram.


```r
hist(upd_total_steps_perday$Total_Steps_Updated, 
     main = "Histogram of Total Steps per Day", xlab = "Total Steps per Day", 
     col= "blue")  
```

![plot of chunk unnamed-chunk-17](figure/unnamed-chunk-17-1.png) 

Recalculate the mean and median of the new dataset. 


```r
mean(upd_total_steps_perday$Total_Steps_Updated)
```

```
## [1] 10766.19
```

```r
median(upd_total_steps_perday$Total_Steps_Updated)
```

```
## [1] 10766.19
```
The new values for the mean and median are both <b> 10766.19</b>.  These values are 
different than the values from the earlier part of the assignment.  Both the mean
and median have increased as a result of imputing the missing data.  


<h2><b>Are there differences in activity patterns between weekdays and weekends?</b></h2>

1. Create a new factor variable with two levels, indicating "weekday" and 
"weekend"

```r
#Add a new column with the factor variable to indicate the day of the week
merged_activity$DayofWeek<-weekdays(as.Date(merged_activity$date, "%Y-%m%d"))
#Add a new column based on the result of a logical check 
merged_activity$DayType<-ifelse(merged_activity$DayofWeek=="Sunday" | 
                                  merged_activity$DayofWeek == "Saturday", 
                                "Weekend", "Weekday")
```
2. Construct a panel plot containing a time series plot of the 5-minute 
interval (x-axis) and average number of stps taken, averaged across all weekday
days or weekend days (y-axis).  
First we need to summarize the data to show us average number of steps per 
interval.  


```r
upd_avg_steps_perint<-merged_activity %>% group_by(interval, DayType)%>% 
  summarize(mean(updated_steps))
#Tidy dataset by assigning appropriate column names
colnames(upd_avg_steps_perint)<-c("interval", "DayType", "Upd_Avg_Steps")
#View first ten rows of the dataset
head(upd_avg_steps_perint,10)
```

```
## Source: local data frame [10 x 3]
## Groups: interval
## 
##    interval DayType Upd_Avg_Steps
## 1         0 Weekday   2.251153040
## 2         0 Weekend   0.214622642
## 3         5 Weekday   0.445283019
## 4         5 Weekend   0.042452830
## 5        10 Weekday   0.173165618
## 6        10 Weekend   0.016509434
## 7        15 Weekday   0.197903564
## 8        15 Weekend   0.018867925
## 9        20 Weekday   0.098951782
## 10       20 Weekend   0.009433962
```
Next we will create another time series plot, this time grouped by the 
type of day.  (Weekend or WeekDay)

```r
qplot(x= interval, y = Upd_Avg_Steps, data = upd_avg_steps_perint, geom = "line", 
      facets = DayType~., title = "Updated Time Series - Average Steps per Interval", 
      ylab= "Average Steps", xlab = "Interval")
```

![plot of chunk unnamed-chunk-21](figure/unnamed-chunk-21-1.png) 
<h1><b>Conclusion</b></h1>

Based on the results of the final graphy it looks like the time of day peaks are 
similar between weekend and weekdays, but the average number of steps is higher
during these peaks on the weekends.  


