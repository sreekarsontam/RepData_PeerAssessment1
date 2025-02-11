```{r,echo=TRUE}
setwd("C:/Users/sreer/OneDrive/Desktop/R PROG/repdata_data_activity")
activity<-read.csv("activity.csv")
```
##Reading the data set
```{r}
dim(activity)
head(activity)
names(activity)
str(activity)
sum(is.na(activity$steps))/dim(activity)
library(lubridate)
activity$date<-ymd(activity$date)
length(unique(activity$date))
```
##History of the total number of steps taken each day
```{r,echo=TRUE}
stepsPerday<-aggregate(steps~date,data = activity,sum,na.rm=TRUE)
hist(stepsPerday$steps)
```
## Mean and median of the total number of steps taken per day
```{r,echo=TRUE}
meanstepsperday<-mean(stepsPerday$steps)
meanstepsperday

medianstepsperday<-median(stepsPerday$steps)
medianstepsperday
```
##Time series plot of the average number of steps taken
```{r,echo=TRUE}
library(ggplot2)
stepsperInterval<-aggregate(steps~interval,data=activity,mean,na.rm=TRUE)
plot(steps~interval,data=stepsperInterval,type="l")
```
##The 5-minute interval that, on average, contains the maximum number of steps
```{r}
intervalWithMaxNoSteps<-stepsperInterval[which.max(stepsperInterval$steps),]$interval
intervalWithMaxNoSteps
```
##Code to describe and show a strategy for imputing missing data
```{r}
totalValuesMissing<-sum(is.na(activity$steps))
totalValuesMissing

getMeanStepsPerInterval<-function(interval){
    stepsperInterval[stepsperInterval$interval==interval,]$steps
}

activityNoNA<-activity
for(i in 1:nrow(activityNoNA)){
    if(is.na(activityNoNA[i,]$steps)){
        activityNoNA[i,]$steps <- getMeanStepsPerInterval(activityNoNA[i,]$interval)
    }
}
totalStepsPerDayNoNA <- aggregate(steps ~ date, data=activityNoNA, sum)
hist(totalStepsPerDayNoNA$steps)

meanStepsPerDayNoNA <- mean(totalStepsPerDayNoNA$steps)
meanStepsPerDayNoNA
medianStepsPerDayNoNA <- median(totalStepsPerDayNoNA$steps)
medianStepsPerDayNoNA
```

##Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends
```{r}
activityNoNA$date <- as.Date(strptime(activityNoNA$date, format="%Y-%m-%d"))
activityNoNA$day <- weekdays(activityNoNA$date)
for (i in 1:nrow(activityNoNA)) {
    if (activityNoNA[i,]$day %in% c("Saturday","Sunday")) {
        activityNoNA[i,]$day<-"weekend"
    }
    else{
        activityNoNA[i,]$day<-"weekday"
    }
}
stepsByDay <- aggregate(activityNoNA$steps ~ activityNoNA$interval + activityNoNA$day, activityNoNA, mean)

names(stepsByDay) <- c("interval", "day", "steps")
library(lattice)
xyplot(steps ~ interval | day, stepsByDay, type = "l", layout = c(1, 2), 
    xlab = "Interval", ylab = "Number of steps")
```









