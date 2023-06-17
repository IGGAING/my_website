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


```r
#ibrary(ggplot2)

# Create a custom color palette
my_colors <- c("White" = "#FF9999", "Black" = "#666666", "Asian" = "#FFCC99", "Other" = "#3399FF")

# Reorder the data frame by the number of shooters in descending order and eliminate the NA
mass_shootings %>% 
  count(race, sort=TRUE) %>% 
  drop_na(race) %>% 
  mutate(race = fct_reorder(race, n)) %>% 
  
  
# Create the bar chart with ordered bars and custom colors
  ggplot( aes(x = race, y = n, fill = race)) +
  geom_col()+
  scale_fill_manual(values = my_colors) +
  labs(x = "Race", y = "Number of Shooters") +
  ggtitle("Number of Mass Shooters by Race") +
  theme_minimal()+
  theme(legend.position = "none")
```

<img src="/blogs/blo4_files/figure-html/unnamed-chunk-2-1.png" width="648" style="display: block; margin: auto;" />

```r
# Create the bar chart with ordered bars and custom colors in the other way from max to min
ggplot(mass_shootings %>% 
         count(race, sort = TRUE) %>% 
         drop_na(race) %>% 
         mutate(race = fct_reorder(race, n)),
       aes(x = reorder(race, -n), y = n, fill = race)) +
  geom_col() +
  scale_fill_manual(values = my_colors) +
  labs(x = "Race", y = "Number of Shooters") +
  ggtitle("Number of Mass Shooters by Race") +
  theme_minimal() +
  theme(legend.position = "none")
```

<img src="/blogs/blo4_files/figure-html/unnamed-chunk-2-2.png" width="648" style="display: block; margin: auto;" />
Generate a boxplot visualizing the number of total victims, by type of location.


```r
library(ggplot2)

# Create the boxplot
ggplot(mass_shootings, aes(x = location_type, y = total_victims)) +
  geom_boxplot(fill = "steelblue", color = "black") +
  labs(x = "Location", y = "Total Victims") +
  ggtitle("Number of Total Victims by Location Type") +
  theme_minimal()
```

<img src="/blogs/blo4_files/figure-html/unnamed-chunk-3-1.png" width="648" style="display: block; margin: auto;" />

```r
# Preprocess the data to remove the outlier in the "Others" location type to make a better chart 
processed_data <- mass_shootings
processed_data$total_victims[processed_data$location_type == "Other" & processed_data$total_victims == 604] <- NA

# Create the boxplot with outliers, excluding the outlier in the "Others" location type
ggplot(processed_data, aes(x = location_type, y = total_victims)) +
  geom_boxplot(fill = "steelblue", color = "black", outlier.colour = "red", outlier.shape = 16) +
  labs(x = "Location", y = "Total Victims") +
  ggtitle("Number of Total Victims by Location Type") +
  theme_minimal()
```

<img src="/blogs/blo4_files/figure-html/unnamed-chunk-3-2.png" width="648" style="display: block; margin: auto;" />

More open-ended questions

Address the following questions. Generate appropriate figures/tables to support your conclusions.

How many white males with prior signs of mental illness initiated a mass shooting after 2000?


```r
# Filter the dataset based on the specified criteria
filtered_data <- mass_shootings %>%
  filter(race == "White", male == "TRUE", `prior_mental_illness` == "Yes", year > 2000)

# Count the number of filtered cases
num_cases <- nrow(filtered_data)

# Display the result as text
cat("The number of white males with prior signs of mental illness who initiated a mass shooting after 2000 is:", num_cases)
```

```
## The number of white males with prior signs of mental illness who initiated a mass shooting after 2000 is: 22
```

Which month of the year has the most mass shootings? Generate a bar chart sorted in chronological (natural) order (Jan-Feb-Mar- etc) to provide evidence of your answer.


```r
# Define the order of the months
month_order <- c("Jan", "Feb", "Mar", "Apr", "May", "Jun",
                 "Jul", "Aug", "Sep", "Oct", "Nov", "Dec")

# Calculate the count of mass shootings for each month
shootings_by_month <- mass_shootings %>%
  count(month) %>%
  arrange(match(month, month_order))

# Convert the month column to a factor with the desired order
shootings_by_month$month <- factor(shootings_by_month$month, levels = month_order)

# Determine the month with the maximum number of mass shootings
max_month <- shootings_by_month$month[which.max(shootings_by_month$n)]
max_count <- max(shootings_by_month$n)

# Create the bar chart
ggplot(shootings_by_month, aes(x = month, y = n)) +
  geom_bar(stat = "identity", fill = ifelse(shootings_by_month$month == max_month, "red", "steelblue"), color = "black") +
  labs(x = "Month", y = "Number of Mass Shootings") +
  ggtitle("Number of Mass Shootings by Month") +
  theme_minimal() +
  theme(legend.position = "none")
```

<img src="/blogs/blo4_files/figure-html/unnamed-chunk-5-1.png" width="648" style="display: block; margin: auto;" />

How does the distribution of mass shooting fatalities differ between White and Black shooters? What about White and Latino shooters?


```r
# Filter the data for relevant groups (White and Black shooters)
filtered_data <- mass_shootings %>%
  filter(race %in% c("White", "Black"))

# Create a boxplot to compare the distribution of fatalities between White and Black shooters
ggplot(filtered_data, aes(x = race, y = fatalities, fill = race)) +
  geom_boxplot() +
  labs(x = "Shooter Race", y = "Number of Fatalities") +
  ggtitle("Distribution of Mass Shooting Fatalities\n(White vs. Black Shooters)") +
  theme_minimal() +
  theme(legend.position = "none")
```

<img src="/blogs/blo4_files/figure-html/unnamed-chunk-6-1.png" width="648" style="display: block; margin: auto;" />

```r
# Filter the data for relevant groups (White and Latino shooters)
filtered_data <- mass_shootings %>%
  filter(race %in% c("White", "Latino"))

# Create a boxplot to compare the distribution of fatalities between White and Latino shooters
ggplot(filtered_data, aes(x = race, y = fatalities, fill = race)) +
  geom_boxplot() +
  labs(x = "Shooter Race", y = "Number of Fatalities") +
  ggtitle("Distribution of Mass Shooting Fatalities\n(White vs. Latino Shooters)") +
  theme_minimal() +
  theme(legend.position = "none")
```

<img src="/blogs/blo4_files/figure-html/unnamed-chunk-6-2.png" width="648" style="display: block; margin: auto;" />

```r
# Calculate summary statistics
summary_white <- summary(mass_shootings$fatalities[mass_shootings$race == "White"])
summary_black <- summary(mass_shootings$fatalities[mass_shootings$race == "Black"])
summary_latino <- summary(mass_shootings$fatalities[mass_shootings$race == "Latino"])

# Conduct statistical tests
ttest_white_black <- t.test(mass_shootings$fatalities[mass_shootings$race == "White"],
                            mass_shootings$fatalities[mass_shootings$race == "Black"])
ttest_white_latino <- t.test(mass_shootings$fatalities[mass_shootings$race == "White"],
                             mass_shootings$fatalities[mass_shootings$race == "Latino"])

# Calculate effect sizes
effect_size_white_black <- abs(ttest_white_black$estimate / sqrt(ttest_white_black$parameter))
effect_size_white_latino <- abs(ttest_white_latino$estimate / sqrt(ttest_white_latino$parameter))

# Print the results
cat("Summary Statistics for White Shooters:\n")
```

```
## Summary Statistics for White Shooters:
```

```r
print(summary_white)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##     3.0     5.0     6.0     8.8     9.0    58.0      11
```

```r
cat("\nSummary Statistics for Black Shooters:\n")
```

```
## 
## Summary Statistics for Black Shooters:
```

```r
print(summary_black)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##    3.00    4.00    5.00    5.57    6.00   12.00      11
```

```r
cat("\nSummary Statistics for Latino Shooters:\n")
```

```
## 
## Summary Statistics for Latino Shooters:
```

```r
print(summary_latino)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##     3.0     3.0     5.0     4.4     5.0     7.0      11
```

```r
cat("\nT-Test Results (White vs. Black Shooters):\n")
```

```
## 
## T-Test Results (White vs. Black Shooters):
```

```r
print(ttest_white_black)
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  mass_shootings$fatalities[mass_shootings$race == "White"] and mass_shootings$fatalities[mass_shootings$race == "Black"]
## t = 3, df = 85, p-value = 0.007
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  0.88 5.53
## sample estimates:
## mean of x mean of y 
##      8.78      5.57
```

```r
cat("\nT-Test Results (White vs. Latino Shooters):\n")
```

```
## 
## T-Test Results (White vs. Latino Shooters):
```

```r
print(ttest_white_latino)
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  mass_shootings$fatalities[mass_shootings$race == "White"] and mass_shootings$fatalities[mass_shootings$race == "Latino"]
## t = 4, df = 74, p-value = 1e-04
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  2.22 6.53
## sample estimates:
## mean of x mean of y 
##      8.78      4.40
```

```r
cat("\nEffect Size (White vs. Black Shooters):\n")
```

```
## 
## Effect Size (White vs. Black Shooters):
```

```r
print(effect_size_white_black)
```

```
## mean of x mean of y 
##     0.952     0.604
```

```r
cat("\nEffect Size (White vs. Latino Shooters):\n")
```

```
## 
## Effect Size (White vs. Latino Shooters):
```

```r
print(effect_size_white_latino)
```

```
## mean of x mean of y 
##     1.019     0.511
```

```r
Answer <-"Answer: The distribution of mass shooting fatalities differs between White and Black shooters. The summary statistics show that White shooters have a slightly higher mean (8.776) compared to Black shooters (5.571), indicating a potential difference in the average number of fatalities. The Welch's t-test between White and Black shooters reveals a statistically significant difference in means (t = 2.7413, p = 0.007457), suggesting that the distributions are likely different. The effect size (Cohen's d) for this comparison is 0.9516, indicating a moderate effect.

Regarding the distribution of mass shooting fatalities between White and Latino shooters, the summary statistics show that White shooters have a higher mean (8.776) compared to Latino shooters (4.4). The Welch's t-test between White and Latino shooters reveals a statistically significant difference in means (t = 4.0458, p = 0.0001266), suggesting distinct distributions. The effect size (Cohen's d) for this comparison is 1.0195, indicating a substantial effect.

In summary, based on the available information, there are differences in the distribution of mass shooting fatalities between White and Black shooters as well as White and Latino shooters. White shooters tend to have higher average fatalities compared to both Black and Latino shooters, and statistical tests confirm these differences as statistically significant. Effect sizes indicate the practical significance of the observed differences, with moderate to substantial effects in both comparisons."

# Print the answer
cat("\n", Answer)
```

```
## 
##  Answer: The distribution of mass shooting fatalities differs between White and Black shooters. The summary statistics show that White shooters have a slightly higher mean (8.776) compared to Black shooters (5.571), indicating a potential difference in the average number of fatalities. The Welch's t-test between White and Black shooters reveals a statistically significant difference in means (t = 2.7413, p = 0.007457), suggesting that the distributions are likely different. The effect size (Cohen's d) for this comparison is 0.9516, indicating a moderate effect.
## 
## Regarding the distribution of mass shooting fatalities between White and Latino shooters, the summary statistics show that White shooters have a higher mean (8.776) compared to Latino shooters (4.4). The Welch's t-test between White and Latino shooters reveals a statistically significant difference in means (t = 4.0458, p = 0.0001266), suggesting distinct distributions. The effect size (Cohen's d) for this comparison is 1.0195, indicating a substantial effect.
## 
## In summary, based on the available information, there are differences in the distribution of mass shooting fatalities between White and Black shooters as well as White and Latino shooters. White shooters tend to have higher average fatalities compared to both Black and Latino shooters, and statistical tests confirm these differences as statistically significant. Effect sizes indicate the practical significance of the observed differences, with moderate to substantial effects in both comparisons.
```
