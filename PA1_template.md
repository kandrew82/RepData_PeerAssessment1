# Reproducible Research: Peer Assessment 1


## Code for reading in the dataset and/or processing the data

Read the unzipped dataset in csv format

```r
activity<-read.csv("activity.csv",stringsAsFactors = FALSE);
```
## Histogram of the total number of steps taken each day

Aggregate total steps by date, then remove NA and draw the histogram


```r
totbyday<-aggregate(activity$steps,by=list(d=activity$date), FUN=sum)
a<-totbyday[!is.na(totbyday$x),]
```

```r
hist(a$x,xlab="Steps By Day",main="")
```

<img src="PA1_template_files/figure-html/unnamed-chunk-3-1.png" style="display: block; margin: auto;" />

## Mean and median number of steps taken each day

Get the summary to view mean and median


```r
summ<-summary(totbyday)
summ[10]
```

```
## [1] "Median :10765  "
```

```r
summ[11]
```

```
## [1] "Mean   :10766  "
```

## Time series plot of the average number of steps taken

Aggregate the average number of steps by date and draw the time series plot


```r
avgbyday<-aggregate(activity$steps,by=list(d=activity$date), FUN=mean)
```

```r
plot.ts(avgbyday$x,xlab= "",ylab="Avg Steps by Day",axes=FALSE)
xticks=c(1,61)
axis(1, at=xticks, labels=c("01/10/2012","30/11/2012"))
axis(2)
```

<img src="PA1_template_files/figure-html/unnamed-chunk-6-1.png" style="display: block; margin: auto;" />

## The 5-minute interval that, on average, contains the maximum number of steps

Remove the NA and group by interval

```r
activity_noNA<-activity[!is.na(activity$steps),]
groupbyinterval<-aggregate(activity_noNA$steps,by=list(d=activity_noNA$interval), FUN=mean)
maxs<-groupbyinterval[which.max(groupbyinterval$x),]
```

The maximum number of steps is

```r
maxs
```

```
##       d        x
## 104 835 206.1698
```

## Code to describe and show a strategy for imputing missing data

Replace each NA with the corresponding 5-minute interval mean

```r
activity_mod<-activity
for (i in 1:nrow(activity_mod)) {
  if (is.na(activity$steps[i])) {
    activity_mod$steps[i]<-as.integer(groupbyinterval[groupbyinterval$d==activity_mod$interval[i],][2])
  }
}
```

## Histogram of the total number of steps taken each day after missing values are imputed

As the first plot but with the new imputed values

```r
totbyday_mod<-aggregate(activity_mod$steps,by=list(d=activity_mod$date), FUN=sum)
```

```r
hist(totbyday_mod$x,xlab="Steps By Day",main="")
```

<img src="PA1_template_files/figure-html/unnamed-chunk-11-1.png" style="display: block; margin: auto;" />

## Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

Separate weekday data from weekend data and compute the average number of steps taken per 5-minute interval


```r
wday<-weekdays(as.Date(activity_mod$date))
activity_mod['wday']<-weekdays(as.Date(activity_mod$date))
activity_mod['weekend']<-activity_mod$wday=='sabato'|activity_mod$wday=='domenica'
activity_weekday<-activity_mod[which(activity_mod$weekend==FALSE),]
activity_weekend<-activity_mod[which(activity_mod$weekend==TRUE),]
avgweekday<-aggregate(activity_weekday$steps,by=list(d=activity_weekday$interval), FUN=mean)
avgweekend<-aggregate(activity_weekend$steps,by=list(d=activity_weekend$interval), FUN=mean)
```

Then plot both series


```r
par(mfrow=c(1,2))
plot.ts(avgweekday$x,xlab= "",ylab="Avg Steps by Day",axes=FALSE,main="WEEKDAYS")
xticks=c(0,144,288)
axis(1, at=xticks, labels=c(0,1155,2355))
axis(2)
plot.ts(avgweekend$x,xlab= "",ylab="Avg Steps by Day",axes=FALSE,main="WEEKEND")
xticks=c(0,144,288)
axis(1, at=xticks, labels=c(0,1155,2355))
axis(2)
```

<img src="PA1_template_files/figure-html/unnamed-chunk-13-1.png" style="display: block; margin: auto;" />

## All of the R code needed to reproduce the results (numbers, plots, etc.) in the report

That's all!
