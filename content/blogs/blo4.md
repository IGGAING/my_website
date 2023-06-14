---
categories:  
- ""    #the front matter should be like the one found in, e.g., blog2.md. It cannot be like the normal Rmd we used
- ""
date: "2021-09-30"
description: Unveiling the Beauty of Data Through Dynamic Visualization # the title that will show up once someone gets to this page
draft: false
image: datavisblog.jpg # save picture in \static\img\blogs. Acceptable formats= jpg, jpeg, or png . Your iPhone pics wont work

keywords: ""
slug: blo4 # slug is the shorthand URL address... no spaces plz
title: Data Visualisation
---
  









# Data Visualisation - Exploration

Now that you've demonstrated your software is setup, and you have the basics of data manipulation, the goal of this assignment is to practice transforming, visualising, and exploring data.

# Mass shootings in the US

In July 2012, in the aftermath of a mass shooting in a movie theater in Aurora, Colorado, Mother Jones published a report on mass shootings in the United States since 1982. Importantly, they provided the underlying data set as an open-source database for anyone interested in studying and understanding this criminal behavior.

# Obtain the data



```
## Rows: 125
## Columns: 14
## $ case                 <chr> "Oxford High School shooting", "San Jose VTA shoo…
## $ year                 <dbl> 2021, 2021, 2021, 2021, 2021, 2021, 2020, 2020, 2…
## $ month                <chr> "Nov", "May", "Apr", "Mar", "Mar", "Mar", "Mar", …
## $ day                  <dbl> 30, 26, 15, 31, 22, 16, 16, 26, 10, 6, 31, 4, 3, …
## $ location             <chr> "Oxford, Michigan", "San Jose, California", "Indi…
## $ summary              <chr> "Ethan Crumbley, a 15-year-old student at Oxford …
## $ fatalities           <dbl> 4, 9, 8, 4, 10, 8, 4, 5, 4, 3, 7, 9, 22, 3, 12, 5…
## $ injured              <dbl> 7, 0, 7, 1, 0, 1, 0, 0, 3, 8, 25, 27, 26, 12, 4, …
## $ total_victims        <dbl> 11, 9, 15, 5, 10, 9, 4, 5, 7, 11, 32, 36, 48, 15,…
## $ location_type        <chr> "School", "Workplace", "Workplace", "Workplace", …
## $ male                 <lgl> TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, T…
## $ age_of_shooter       <dbl> 15, 57, 19, NA, 21, 21, 31, 51, NA, NA, 36, 24, 2…
## $ race                 <chr> NA, NA, "White", NA, NA, "White", NA, "Black", "B…
## $ prior_mental_illness <chr> NA, "Yes", "Yes", NA, "Yes", NA, NA, NA, NA, NA, …
```

Explore the data

Specific questions

Generate a data frame that summarizes the number of mass shootings per year.


```r
# Group the data by year and count the number of shootings
shootings_per_year <- mass_shootings %>%
  group_by(year) %>%
  summarise(Shootings = n())

# View the resulting data frame
print(shootings_per_year)
```

```
## # A tibble: 37 × 2
##     year Shootings
##    <dbl>     <int>
##  1  1982         1
##  2  1984         2
##  3  1986         1
##  4  1987         1
##  5  1988         1
##  6  1989         2
##  7  1990         1
##  8  1991         3
##  9  1992         2
## 10  1993         4
## # ℹ 27 more rows
```
Generate a bar chart that identifies the number of mass shooters associated with each race category. The bars should be sorted from highest to lowest and each bar should show its number.

<img src="/blogs/blo4_files/figure-html/risk_return-1.png" width="648" style="display: block; margin: auto;" /><img src="/blogs/blo4_files/figure-html/risk_return-2.png" width="648" style="display: block; margin: auto;" />
