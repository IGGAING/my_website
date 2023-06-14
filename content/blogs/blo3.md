---
categories:  
- ""    #the front matter should be like the one found in, e.g., blog2.md. It cannot be like the normal Rmd we used
- ""
date: "2021-09-30"
description: Data Manipulation with flights data base # the title that will show up once someone gets to this page
draft: false
image: datablog.jpg # save picture in \static\img\blogs. Acceptable formats= jpg, jpeg, or png . Your iPhone pics wont work

keywords: ""
slug: blo3 # slug is the shorthand URL address... no spaces plz
title: Data Manipulation
---
  









# Logical operators with flight database

Problem 1: Use logical operators to find flights that:
-   Had an arrival delay of two or more hours (\> 120 minutes)
-   Flew to Houston (IAH or HOU)
-   Were operated by United (`UA`), American (`AA`), or Delta (`DL`)
-   Departed in summer (July, August, and September)
-   Arrived more than two hours late, but didn't leave late
-   Were delayed by at least an hour, but made up over 30 minutes in flight



```r
# Had an arrival delay of two or more hours (> 120 minutes)
flights %>% 
  filter(arr_delay >= 2)
```

```
## # A tibble: 127,929 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      517            515         2      830            819
##  2  2013     1     1      533            529         4      850            830
##  3  2013     1     1      542            540         2      923            850
##  4  2013     1     1      554            558        -4      740            728
##  5  2013     1     1      555            600        -5      913            854
##  6  2013     1     1      558            600        -2      753            745
##  7  2013     1     1      558            600        -2      924            917
##  8  2013     1     1      559            600        -1      941            910
##  9  2013     1     1      600            600         0      837            825
## 10  2013     1     1      602            605        -3      821            805
## # ℹ 127,919 more rows
## # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
## #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

Problem 2: What months had the highest and lowest proportion of cancelled flights? Interpret any seasonal patterns. To determine if a flight was cancelled use the following code


```r
#library(dplyr)

# What months had the highest and lowest % of cancelled flights?

# Calculate the percentage of cancelled flights per month
cancelled_flights_per_month <- flights %>% 
  group_by(month) %>%
  summarise(total_flights = n(), 
            cancelled_flights = sum (is.na(dep_time))) %>% 
  mutate(percentage_cancelled = round(cancelled_flights / total_flights * 100,2))

# Display the results
cancelled_flights_per_month
```

```
## # A tibble: 12 × 4
##    month total_flights cancelled_flights percentage_cancelled
##    <int>         <int>             <int>                <dbl>
##  1     1         27004               521                 1.93
##  2     2         24951              1261                 5.05
##  3     3         28834               861                 2.99
##  4     4         28330               668                 2.36
##  5     5         28796               563                 1.96
##  6     6         28243              1009                 3.57
##  7     7         29425               940                 3.19
##  8     8         29327               486                 1.66
##  9     9         27574               452                 1.64
## 10    10         28889               236                 0.82
## 11    11         27268               233                 0.85
## 12    12         28135              1025                 3.64
```

```r
# Find the month with the maximum and minimum percentage of cancelled flights
month_with_max_cancelled <- which.max(cancelled_flights_per_month$percentage_cancelled)
month_with_min_cancelled <- which.min(cancelled_flights_per_month$percentage_cancelled)

# Display the month with the maximum and minimum percentage of cancelled flights
cat("The month with the maximum percentage of cancelled flights was:", month.name[month_with_max_cancelled], "\n")
```

```
## The month with the maximum percentage of cancelled flights was: February
```

```r
cat("The month with the minimum percentage of cancelled flights was:", month.name[month_with_min_cancelled], "\n")
```

```
## The month with the minimum percentage of cancelled flights was: October
```
Problem 3: What plane (specified by the tailnum variable) traveled the most times from New York City airports in 2013? Please left_join() the resulting table with the table planes (also included in the nycflights13 package).


```
## [1] "N328AA"
```

```
## # A tibble: 393 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1     1026           1030        -4     1351           1340
##  2  2013     1     2     1038           1030         8     1347           1340
##  3  2013     1     3     1152           1200        -8     1446           1510
##  4  2013     1     4      858            900        -2     1210           1220
##  5  2013     1     5      851            900        -9     1206           1220
##  6  2013     1     6     1027           1030        -3     1335           1340
##  7  2013     1     7      724            730        -6     1008           1100
##  8  2013     1     7     2134           2135        -1       19             50
##  9  2013     1     8     2130           2135        -5      114             50
## 10  2013     1     9     1701           1645        16     1958           2005
## # ℹ 383 more rows
## # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
## #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

```
## # A tibble: 6 × 2
##   dest      n
##   <chr> <int>
## 1 LAX     313
## 2 SFO      52
## 3 MIA      25
## 4 BOS       1
## 5 MCO       1
## 6 SJU       1
```
