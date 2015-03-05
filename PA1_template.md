# Reproducible Research: Peer Assessment 1


<!--- ============================================================================= --->
## Loading and preprocessing the data

To load the data from the .csv file (which is located in a subfolder `activity/`) into a variable `rawdata`, the following command is used:


```r
rawdata <- read.csv("activity/activity.csv")
```

After loading the data, it has to be prepared for the following analyses by removing the NA values from the data:


```r
procdata <- rawdata[!is.na(rawdata["steps"]),]
```



<!--- ============================================================================= --->
## What is mean total number of steps taken per day?

As a basis for the calculation of mean and median, the sum of steps for each day is calculated by aggregating values obtained on the same day:


```r
totalNumberStepsPerDay <- aggregate(procdata$steps, by=list(procdata$date), FUN=sum)
```

A histogram of can be plotted using he hist function:


```r
names(totalNumberStepsPerDay) <- c("Date", "Steps_total")
hist(totalNumberStepsPerDay$Steps_total, breaks=25, main="Histogram of total number of steps per day", xlab="Total number of steps per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 


The mean and median of the total number of steps are calculated:


```r
meanStepsIgnoredNAs <- mean(totalNumberStepsPerDay$Steps_total)
medianStepsIgnoredNAs <- median(totalNumberStepsPerDay$Steps_total)
```

And the results are as follows:


```r
meanStepsIgnoredNAs
```

```
## [1] 10766.19
```

```r
medianStepsIgnoredNAs
```

```
## [1] 10765
```



<!--- ============================================================================= --->
## What is the average daily activity pattern?

To analyze the daily activity pattern, we first have to calculate the average on all 5min periods:


```r
fiveMinAvg <- aggregate(procdata$steps, by=list(procdata$interval), FUN=mean)
```

Now we plot these average as a time series:


```r
names(fiveMinAvg) <- c("interval", "steps_avg")
plot(fiveMinAvg, type="l", xlab="5min interval", ylab="Mean number of steps", main="Average number of steps over 5min intervals of a day")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png) 

The maximum number of steps (on average) of a whole day falls into the following 5min interval (found by selecting the entry with the maximum number of average steps, and printing the number of steps from this line):


```r
fiveMinAvg[which.max(fiveMinAvg$steps_avg),][1,1]
```

```
## [1] 835
```


<!--- ============================================================================= --->
## Inputing missing values

The number of rows with missing values in the data set is (calculated similarly to :


```r
length(rawdata[is.na(rawdata["steps"]),]$steps)
```

```
## [1] 2304
```


To fill in the missing values, the NA will be replaced with the median of the missing time interval over all days. The filled data set is calculated like this:


```r
fiveMinMedian <- aggregate(procdata$steps, by=list(procdata$interval), FUN=median)
names(fiveMinMedian) <- c("interval", "steps_median")

filleddata <- rawdata
for (i in 1:nrow(filleddata)){
  if (is.na(filleddata[i, "steps"])){
    filleddata[i, "steps"] <- fiveMinMedian[fiveMinMedian[,"interval"]==filleddata[i, "interval"], "steps_median"]
  }
}
```


### Redo of total number of steps analysis steps

To compare this new data with replaced NAs with the previously used data, the calculations and steps from previously is repeated with the new data.


First, calculate the sum of steps for each day and plot it as a histogram:


```r
totalNumberStepsPerDayReplNa <- aggregate(filleddata$steps, by=list(filleddata$date), FUN=sum)
names(totalNumberStepsPerDayReplNa) <- c("Date", "Steps_total")
hist(totalNumberStepsPerDayReplNa$Steps_total, breaks=25, main="Histogram of total number of steps per day (with replaced NAs)", xlab="Total number of steps per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png) 


The mean and median of the total number of steps are calculated:


```r
meanStepsReplacedNAs <- mean(totalNumberStepsPerDayReplNa$Steps_total)
medianStepsReplacedNAs <- median(totalNumberStepsPerDayReplNa$Steps_total)
```

And the results are as follows:


```r
meanStepsReplacedNAs
```

```
## [1] 9503.869
```

```r
medianStepsReplacedNAs
```

```
## [1] 10395
```


### Comparison of results with ignored NAs and replaced NAs

Comparison of both means:


```r
stepMeans <- c(meanStepsIgnoredNAs, meanStepsReplacedNAs)
names(stepMeans) <- c("Ignore_NAs", "Replaced_NAs")
stepMeans
```

```
##   Ignore_NAs Replaced_NAs 
##    10766.189     9503.869
```

And the same for the medians:


```r
stepMedians <- c(medianStepsIgnoredNAs, medianStepsReplacedNAs)
names(stepMedians) <- c("Ignore_NAs", "Replaced_NAs")
stepMedians
```

```
##   Ignore_NAs Replaced_NAs 
##        10765        10395
```

Apparently, mean as well as median are higher if the entries *with* an NA value are just ignored. When looking more deeply into the matter, one reason for this can be found: When ignoring the NA entries, there are whole days missing:


```r
length(totalNumberStepsPerDay$Date)
```

```
## [1] 53
```

vs.


```r
length(totalNumberStepsPerDayReplNa$Date)
```

```
## [1] 61
```





## Are there differences in activity patterns between weekdays and weekends?
