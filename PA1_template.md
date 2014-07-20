###Introduction
This document gives the analysis of two month data for number of steps taken in every five minute period

R codes are incorporated to provide the traceability of the analysis

Each code chunk can be independantly run, starting form '.CSV' file downloading, to trace the results provided in this doucment

### Raw data statistics

- Period of data Oct 1, 2012 to Nov 30, 2012 (61 days)
- Total observations 17568
- Valid observations 15264
- Observation with NA value for steps  2304
- 8 days data is stored with NA records and 53 days have all valid records

### TASK 1: Mean value of number of steps taken per day for two month data

**1. Results**

NA values omitted for this analysis

- Mean value  10766
- Median value 10765
- Histogram generated is the frequency diagram for total number of steps taken in a day for the valid 
two month data 

**2. Code explanation**

- Raw data (.csv file) imported into a data table
- NA record are removed for the first part of the analyis
- Total steps for each day is calculated
- Histogram plotted to give frequency of total steps taken in a day for the entire valid data
- Plot stored as 'task1.png'
- Mean and median value are computed and printed

**3. R_code**


```r
library("data.table")
```

```
## Warning: package 'data.table' was built under R version 3.1.0
```

```r
library("ggplot2")
```

```
## Warning: package 'ggplot2' was built under R version 3.0.3
```

```r
  DT_rawdata<-data.table(read.csv("./activity.csv"))
##removing NA rows
  DT_noNA<-subset(DT_rawdata,!is.na(steps))
##summing steps by date and naming new column
  DT_tot <- DT_noNA[,sum(steps),by=date]
  setnames(DT_tot,"V1","tot_steps")
##Plotting the histogram
  png(filename = "task1.png", width = 480, height = 480, units = "px")
  plot <-ggplot(DT_tot,aes(tot_steps))+geom_histogram(colour = "green",binwidth =750)
  plot = plot +labs(y = "Frequency",x="Total steps / day - two months period from Oct 1, 2012 to Nov 31, 2012")
  print(plot)
  dev.off()
```

```
## pdf 
##   2
```

```r
## computing mean and median and printing
  mean_1<- round(mean(DT_tot$tot_steps), digit = 0)
  median_1 <- median(DT_tot$tot_steps)
  paste("Mean value of steps / day for two months:",mean_1)
```

```
## [1] "Mean value of steps / day for two months: 10766"
```

```r
  paste("Median value of steps / day for two months:",median_1)
```

```
## [1] "Median value of steps / day for two months: 10765"
```

###TASK 2: Average daily activity pattern

**1. Results**

NA values omitted for this analysis

- Maximum number of steps in five minute interval, averaged for all days, is 10927 steps / day
- This maximum occurs at the five minute time slot No. 103  
- This correspond to time between 8:35 to 8:40 Hrs

**2. Code explanation**

- Raw data (.csv file) imported into a data table
- NA record are removed for the first part of the analysis
- Total steps for each time interval is calculated for all days, resulting in 288 observation for 288 five minutes interval in a day
- for the 288 intervals, sequence from 0 to 287 is generated to represent the time slot
- This time slot sequence and total steps for each slot used to create the time series chart
- chart stored as 'task2.png'

**3. R_code**


```r
library("data.table")
library("ggplot2")
## Raw date read, NA records removed 
  DT_rawdata<-data.table(read.csv("./activity.csv"))
  DT_noNA<-subset(DT_rawdata,!is.na(steps))
## Total steps of all days for each time interval computed 
  DT_tot <- DT_noNA[,sum(steps),by=interval]
##For 288 five minute slots in a day, sequence of numbers from 0 to 287 generated
  Time_slots<- seq(0,287,1)
## preparing data and plotting 
  req_data<-data.frame(Time_slot =Time_slots,tot_steps=DT_tot$V1)
  png(filename = "task2.png", width = 480, height = 480, units = "px")
  plot <- ggplot(req_data, aes(x=Time_slot, y=tot_steps))+geom_line(color = "blue")
  plot = plot + ggtitle("Time Series")+scale_y_continuous(name="Average No. of Steps")
  plot = plot +scale_x_continuous(name="Slot No.of five minute interval in a day")
  print(plot)
  dev.off()
```

```
## pdf 
##   2
```

```r
## gathering and printing data analysis
  max_dat<-subset(req_data,req_data$tot_steps==max(req_data$tot_steps))
  Max_slot = max_dat$Time_slot
  Hrs <- trunc(Max_slot*5/60)
  Min <- ((Max_slot*5/60)-Hrs)*60
  paste("Maximum average steps for two month occurs at",Max_slot,"slot",sep=" ")
```

```
## [1] "Maximum average steps for two month occurs at 103 slot"
```

```r
  paste("This coresponds to five minutes duration starting at",Hrs,"Hrs:",Min,"minutes",sep = " ")
```

```
## [1] "This coresponds to five minutes duration starting at 8 Hrs: 35 minutes"
```

```r
  paste("Maximum average steps for the time period for two months is",max_dat$tot_steps, sep = " ")
```

```
## [1] "Maximum average steps for the time period for two months is 10927"
```

### TASK 3: Populating empty ('NA') values

**1. Results**

- Total number of missing value observations: 2304
- Average steps for a slot, computed from valid two month data, is used as the strategy for populating missing values for that slot
- There is no appreciable change in mean and median values after populating missing values 
- There is no change in mean value before and after clearing NA cases
- Median value has decreased by 3 from 10765 steps after clearing NA cases and this is negligible

**Impact on daily number of steps**

- NA records counted for each day and observed as 8 complete dates
- populating steps values to NA record has given total steps / day as 10762 for these 8 days
- This has resulted only in the frequency increase by 8 for total steps / day for the range 10500 to 11250
- Frequency for all other ranges are same as in the case where NA records were omitted

**2. Code explanation**

- Raw data (.csv file) imported into a data table
- Valid and NA records are loaded onto two different data tables
- Mean and median values for valid records computed for comparison after correcting NA records
- As per the strategy for each time interval average steps calculated using valid data of two months
- NA records updated with this time interval value
- Frequency diagram for total steps per day plotted as histogram
- Diagram stored as 'task3.png'
- New mean and median after updating NA records computed
- New and old men and median value compared and results printed
- For NA observation no of observation for each day counted and found as 288 ( whole day data)
- Steps for these days counted and found to be 10762 for all days

**3. R_code**


```r
library("data.table")
library("ggplot2")
  DT_rawdata<-data.table(read.csv("./activity.csv"))
  na_cases<-sum(is.na(DT_rawdata$steps))
  paste("Total number of missing value obesrvation:",na_cases,sep = " ")
```

```
## [1] "Total number of missing value obesrvation: 2304"
```

```r
## Loading NA recs and valid recs into two data tables by subsetting
  na_recs<-subset(DT_rawdata,is.na(steps))
  valid_recs<-subset(DT_rawdata,!is.na(steps))
## Computing mean and median for valid observations
  DT_tot <- valid_recs[,sum(steps),by = date]
  mean_1 <- round(mean(DT_tot$V1),digit= 0)
  median_1 <- median(DT_tot$V1)
  setkey(valid_recs,interval)
## For each time interval, computing average steps with valid record of all days
  slot_mean <- valid_recs[,round(mean(steps),digits = 0),by=interval]
## Updating NA records with this average value of steps for each interval 
  na_recs[,steps:=as.integer(slot_mean$V1)]
```

```
##       steps     date interval
##    1:     2 01-10-12        0
##    2:     0 01-10-12        5
##    3:     0 01-10-12       10
##    4:     0 01-10-12       15
##    5:     0 01-10-12       20
##   ---                        
## 2300:     5 30-11-12     2335
## 2301:     3 30-11-12     2340
## 2302:     1 30-11-12     2345
## 2303:     0 30-11-12     2350
## 2304:     1 30-11-12     2355
```

```r
## combining valid records and updated na records
  tt <- rbind(valid_recs,na_recs)
## Preparing data for plotting  
  png(filename = "task3.png", width = 480, height = 480, units = "px")
  DT_tot <- tt[,sum(steps),by=date]
  setkey(tt,date)
  ggplot(DT_tot,aes(V1))+geom_histogram(colour = "green",binwidth =750)+labs(y = "Frequency",x="total steps / day")
  dev.off()
```

```
## pdf 
##   2
```

```r
##Calculating new mean and median
  mean_2 <- round(mean(DT_tot$V1),digit= 0)
  median_2 <- median(DT_tot$V1)
## Display values desired
  paste("Mean value of steps/ day for two months:",mean_2)
```

```
## [1] "Mean value of steps/ day for two months: 10766"
```

```r
  paste("Median value of steps/ day for two months:",median_2)
```

```
## [1] "Median value of steps/ day for two months: 10762"
```

```r
  paste("Mean value has increase by",(mean_2 - mean_1),"steps due to populating NA values")
```

```
## [1] "Mean value has increase by 0 steps due to populating NA values"
```

```r
  paste("Median value has decreased by",(median_1 - median_2),"steps due to populating NA values")
```

```
## [1] "Median value has decreased by 3 steps due to populating NA values"
```

```r
## Finding impact of claring NA records on steps per day
  print( na_recs[,length(interval),by = "date"])
```

```
##        date  V1
## 1: 01-10-12 288
## 2: 08-10-12 288
## 3: 01-11-12 288
## 4: 04-11-12 288
## 5: 09-11-12 288
## 6: 10-11-12 288
## 7: 14-11-12 288
## 8: 30-11-12 288
```

```r
  print( na_recs[,sum(steps),by = date]-> DT_tot_na)
```

```
##        date    V1
## 1: 01-10-12 10762
## 2: 08-10-12 10762
## 3: 01-11-12 10762
## 4: 04-11-12 10762
## 5: 09-11-12 10762
## 6: 10-11-12 10762
## 7: 14-11-12 10762
## 8: 30-11-12 10762
```

###TASK 4: Differences in activity patterns between weekdays and weekends
Codes for populating NA values with average steps per slot included in the code chunk

This will enable independent running of the code

**1. Results**

- Graph show that No. of steps on week days are more and fluctuating more particularly during day time
- The variation is 4.69 times when compared to week end and occurs at the time slot 8:35 to 8:40 hrs (time slot No.103, same as in task 2)
- On week day this maximum average value is 10366 steps for this time period
- For the same time period week end average steps are 2209

**2. Code Explanation**

- Raw data (.csv file) imported into a data table
- All All steps exampling the program are incorporated in the code chunk as comment
- genrated graph is stored as 'task4.png'

**3. R_code**


```r
library(data.table)
library("ggplot2")

  DT_rawdata<-data.table(read.csv("./activity.csv"))
##For 288 five minute slots in a day, sequence of numbers from 0 to 287 generated
  Time_slots<- seq(0,287,1)
##Duplicating this slots for 61 days data
  raw_timeslot<-rep(Time_slots, 61)
##Time slot colum added to raw data
  DT_rawdata[,time_slot:=raw_timeslot]
```

```
##        steps     date interval time_slot
##     1:    NA 01-10-12        0         0
##     2:    NA 01-10-12        5         1
##     3:    NA 01-10-12       10         2
##     4:    NA 01-10-12       15         3
##     5:    NA 01-10-12       20         4
##    ---                                  
## 17564:    NA 30-11-12     2335       283
## 17565:    NA 30-11-12     2340       284
## 17566:    NA 30-11-12     2345       285
## 17567:    NA 30-11-12     2350       286
## 17568:    NA 30-11-12     2355       287
```

```r
## Loading NA recs and valid recs into two data tables by subsetting
  na_recs<-subset(DT_rawdata,is.na(steps))
  valid_recs<-subset(DT_rawdata,!is.na(steps))
## For each time interval, computing average steps with valid record of all days
  slot_mean <- valid_recs[,round(mean(steps),digits = 0),by=interval]
## Updating NA records with this average value of steps for each interva
  na_recs[,steps:=as.integer(slot_mean$V1)]
```

```
##       steps     date interval time_slot
##    1:     2 01-10-12        0         0
##    2:     0 01-10-12        5         1
##    3:     0 01-10-12       10         2
##    4:     0 01-10-12       15         3
##    5:     0 01-10-12       20         4
##   ---                                  
## 2300:     5 30-11-12     2335       283
## 2301:     3 30-11-12     2340       284
## 2302:     1 30-11-12     2345       285
## 2303:     0 30-11-12     2350       286
## 2304:     1 30-11-12     2355       287
```

```r
## combining valid records and updated na records
  tt <- rbind(valid_recs,na_recs)
  tt[,dt:=as.Date(date,format("%d-%m-%y"))]
```

```
##        steps     date interval time_slot         dt
##     1:     0 02-10-12        0         0 2012-10-02
##     2:     0 02-10-12        5         1 2012-10-02
##     3:     0 02-10-12       10         2 2012-10-02
##     4:     0 02-10-12       15         3 2012-10-02
##     5:     0 02-10-12       20         4 2012-10-02
##    ---                                             
## 17564:     5 30-11-12     2335       283 2012-11-30
## 17565:     3 30-11-12     2340       284 2012-11-30
## 17566:     1 30-11-12     2345       285 2012-11-30
## 17567:     0 30-11-12     2350       286 2012-11-30
## 17568:     1 30-11-12     2355       287 2012-11-30
```

```r
##To the output from  'wday' function '1' is added to make Sunday as 1 Monday as 2 ....Saturday 7
  yy1 <- as.POSIXlt(tt$dt)$wday+1
##weekday, weekend factor variable colum added to data table
  tt[,flg:=yy1 ]
```

```
##        steps     date interval time_slot         dt flg
##     1:     0 02-10-12        0         0 2012-10-02   3
##     2:     0 02-10-12        5         1 2012-10-02   3
##     3:     0 02-10-12       10         2 2012-10-02   3
##     4:     0 02-10-12       15         3 2012-10-02   3
##     5:     0 02-10-12       20         4 2012-10-02   3
##    ---                                                 
## 17564:     5 30-11-12     2335       283 2012-11-30   6
## 17565:     3 30-11-12     2340       284 2012-11-30   6
## 17566:     1 30-11-12     2345       285 2012-11-30   6
## 17567:     0 30-11-12     2350       286 2012-11-30   6
## 17568:     1 30-11-12     2355       287 2012-11-30   6
```

```r
  tt[flg>1 & flg < 7,flg1:="week Day"]
```

```
##        steps     date interval time_slot         dt flg     flg1
##     1:     0 02-10-12        0         0 2012-10-02   3 week Day
##     2:     0 02-10-12        5         1 2012-10-02   3 week Day
##     3:     0 02-10-12       10         2 2012-10-02   3 week Day
##     4:     0 02-10-12       15         3 2012-10-02   3 week Day
##     5:     0 02-10-12       20         4 2012-10-02   3 week Day
##    ---                                                          
## 17564:     5 30-11-12     2335       283 2012-11-30   6 week Day
## 17565:     3 30-11-12     2340       284 2012-11-30   6 week Day
## 17566:     1 30-11-12     2345       285 2012-11-30   6 week Day
## 17567:     0 30-11-12     2350       286 2012-11-30   6 week Day
## 17568:     1 30-11-12     2355       287 2012-11-30   6 week Day
```

```r
  tt[flg==7|flg==1, flg1:="Week end" ]
```

```
##        steps     date interval time_slot         dt flg     flg1
##     1:     0 02-10-12        0         0 2012-10-02   3 week Day
##     2:     0 02-10-12        5         1 2012-10-02   3 week Day
##     3:     0 02-10-12       10         2 2012-10-02   3 week Day
##     4:     0 02-10-12       15         3 2012-10-02   3 week Day
##     5:     0 02-10-12       20         4 2012-10-02   3 week Day
##    ---                                                          
## 17564:     5 30-11-12     2335       283 2012-11-30   6 week Day
## 17565:     3 30-11-12     2340       284 2012-11-30   6 week Day
## 17566:     1 30-11-12     2345       285 2012-11-30   6 week Day
## 17567:     0 30-11-12     2350       286 2012-11-30   6 week Day
## 17568:     1 30-11-12     2355       287 2012-11-30   6 week Day
```

```r
##susetting week days and week ends and computing time slot total for all days
  kk <- subset(tt,flg1=="week Day")
  kk1 <- subset(tt,flg1=="Week end")
  DT_week_tot <- kk[,sum(steps),by=interval]
  DT_wkend_tot <-kk1[,sum(steps),by=interval]
##Preparing required data for the graph
  req_data_wk<-data.frame(Time_slot =Time_slots,tot_steps=DT_week_tot$V1,flg="Weekday")
  req_data_wkend<-data.frame(Time_slot =Time_slots,tot_steps=DT_wkend_tot$V1,flg="Weekend")
  reqdata<-rbind(req_data_wk,req_data_wkend)
  png(filename = "task4.png", width = 480, height = 480, units = "px")
  g <- ggplot(reqdata, aes(Time_slot, tot_steps))
  g= g + geom_line(alpha = 1/3)
  g= g  + facet_grid(flg~.)
  g=g +ggtitle("Time Series")+scale_y_continuous(name="Number of Steps")
  g = g +scale_x_continuous(name="Five minutes Time interval")
  print(g)
  dev.off()
```

```
## pdf 
##   2
```

```r
##analysing the data
  max_val <-max(req_data_wk$tot_steps)
  max_slot <- req_data_wk$Time_slot[req_data_wk$tot_steps==max_val]
  wkend_val <-req_data_wkend$tot_steps[req_data_wkend == max_slot]
  max_val/wkend_val
```

```
## [1] 4.693
```

```r
  Hrs <- trunc(max_slot*5/60)
  Min <- ((max_slot*5/60)-Hrs)*60
  paste("maximum steps in week days is:",max_val,sep = " ")
```

```
## [1] "maximum steps in week days is: 10366"
```

```r
  paste("this valuue occurs at slot number:",max_slot)
```

```
## [1] "this valuue occurs at slot number: 103"
```

```r
  paste("This slot corresponds to five minute time slot starting at", Hrs,":",Min, "Hrs")
```

```
## [1] "This slot corresponds to five minute time slot starting at 8 : 35 Hrs"
```

```r
  paste("week end value for this time slot:", wkend_val)
```

```
## [1] "week end value for this time slot: 2209"
```

```r
  rr <- round(max_val/wkend_val,2)
  paste("week value is more than week end value by", rr, "times")
```

```
## [1] "week value is more than week end value by 4.69 times"
```



