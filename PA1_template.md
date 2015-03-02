# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

To load the data from the .csv file (which is located in a subfolder `activity/`) into a variable `rawdata`, the following command is used:


```r
rawdata <- read.csv("activity/activity.csv")
```

After loading the data, it has to be prepared for the following analyses by removing the NA values from the data:


```r
procdata <- rawdata[!is.na(rawdata["steps"]),]
```



## What is mean total number of steps taken per day?



## What is the average daily activity pattern?



## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
