---
categories:  
- ""    #the front matter should be like the one found in, e.g., blog2.md. It cannot be like the normal Rmd we used
- ""
date: "2021-09-30"
description: Unleashing Insights through Captivating Analysis # the title that will show up once someone gets to this page
draft: false
image: dataa2.jpg # save picture in \static\img\blogs. Acceptable formats= jpg, jpeg, or png . Your iPhone pics wont work

keywords: ""
slug: blo5 # slug is the shorthand URL address... no spaces plz
title: Data Analysis
---
  









# Money in UK politics

[The Westminster Accounts](https://news.sky.com/story/the-westminster-accounts-12786091), a recent collaboration between Sky News and Tortoise Media, examines the flow of money through UK politics. It does so by combining data from three key sources:

1.  [Register of Members' Financial Interests](https://www.parliament.uk/mps-lords-and-offices/standards-and-financial-interests/parliamentary-commissioner-for-standards/registers-of-interests/register-of-members-financial-interests/),
2.  [Electoral Commission records of donations to parties](http://search.electoralcommission.org.uk/English/Search/Donations), and
3.  [Register of All-Party Parliamentary Groups](https://www.parliament.uk/mps-lords-and-offices/standards-and-financial-interests/parliamentary-commissioner-for-standards/registers-of-interests/register-of-all-party-party-parliamentary-groups/).

You can [search and explore the results](https://news.sky.com/story/westminster-accounts-search-for-your-mp-or-enter-your-full-postcode-12771627) through the collaboration's interactive database. Simon Willison [has extracted a database](https://til.simonwillison.net/shot-scraper/scraping-flourish) and this is what we will be working with. If you want to read more about [the project's methodology](https://www.tortoisemedia.com/2023/01/08/the-westminster-accounts-methodology/).

## Open a connection to the database

The database made available by Simon Willison is an `SQLite` database



```r
 con <- DBI::dbConnect(
  drv = RSQLite::SQLite(),
  dbname = here::here("data", "sky-westminster-files.db")
)
```

How many tables does the database have?


```r
DBI::dbListTables(con)
```

```
## character(0)
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
