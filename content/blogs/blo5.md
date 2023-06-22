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
#DBI::dbListTables(con)
```
## Which MP has received the most amount of money?

You need to work with the `payments` and `members` tables and for now we just want the total among all years. To insert a new, blank chunk of code where you can write your beautiful code (and comments!), please use the following shortcut: `Ctrl + Alt + I` (Windows) or `cmd + option + I` (mac)


```r
#Code with SQL 
# Retrieve the data from the 'payments' table
payments <- dbGetQuery(con, "SELECT * FROM payments")

# Retrieve the data from the 'members' table
members <- dbGetQuery(con, "SELECT * FROM members")

# SQL query to order the donations by MP and order desc, total_money will be in million dollars
donations_by_MP <- dbGetQuery(con, "
SELECT m.id, m.name, (SUM(p.value)/1e6) as total_money
FROM payments p
JOIN members m ON p.member_id = m.id
GROUP BY m.id, m.name
ORDER BY total_money DESC
")

# Retrieve the first row of the result
# This represents the MP who has received the highest amount of money
top_MP <- donations_by_MP[1, ]

# Combine all information into one summary string
output <- paste0("The MP who received the most amount of money is ", top_MP$name, 
                 " with a total of ", round(top_MP$total_money, 2), " million dollars.")

output
```

```
## [1] "The MP who received the most amount of money is Theresa May with a total of 2.81 million dollars."
```


```r
#Code using R programming language
# Retrieve data from the 'payments_db' table and store it in 'payments'
payments <- tbl(con, "payments") %>%
  collect()

# Retrieve data from the 'members_db' table and store it in 'members'
members <- tbl(con, "members") %>%
  collect()

# Retrieve data from the 'member_appgs' table and store it in 'member_appgs'
member_appgs <- tbl(con, "member_appgs") %>%
  collect()

# Retrieve data from the 'parties' table and store it in 'parties'
parties <- tbl(con, "parties") %>%
  collect()

# Retrieve data from the 'party_donations' table and store it in 'party_donations'
party_donations <- tbl(con, "party_donations") %>%
  collect()

# Join 'payments' and 'members' dataframes using 'member_id' and 'id' columns
# Group the data by 'member_id' and 'name'
# Summarize the data by adding up the 'value' column for each group and convert to millions of dollars
# Arrange the data in descending order of 'total_money'
donations_by_MP <- payments %>%
  inner_join(members, by = c("member_id" = "id")) %>%
  group_by(member_id, name) %>%
  summarise(total_money = sum(value)/1e6) %>%
  arrange(desc(total_money))

# Retrieve the first row of the result
# This represents the MP who has received the highest amount of money
top_MP <- donations_by_MP[1, ]

# Combine all information into one summary string
output <- paste0("The MP who received the most amount of money is ", top_MP$name, 
                 " with a total of ", round(top_MP$total_money, 2), " million dollars.")

output
```

```
## [1] "The MP who received the most amount of money is Theresa May with a total of 2.81 million dollars."
```

## Any `entity` that accounts for more than 5% of all donations?

Is there any `entity` whose donations account for more than 5% of the total payments given to MPs over the 2020-2022 interval? Who are they and who did they give money to?


```r
# Store result of data transformation pipeline in 'summary'
summary <- party_donations %>% 
  rename(id = party_id) %>% # Rename 'party_id' column to 'id'
  left_join(parties, by="id") %>% 
  group_by(entity, name) %>% 
  summarise(sumvalue=sum(value)/1e6, .groups = "drop") %>% # Sum for each group (in millions), drop groups after summarization
  mutate(percentage = sumvalue/sum(sumvalue)*100) %>% # Compute 'percentage' for each entity
  filter(percentage > 5) %>% # Only keep entities with 'percentage' more than 5
  arrange(desc(percentage)) # Sort data in descending order by 'percentage'

# Get the number of entities
n_entities <- nrow(summary)

# Create a string for each entity combining their name, donation amount in millions, and percentage
entity_info <- paste0(summary$name, " donated ", round(summary$sumvalue, 2), 
                      " million(s), representing ", round(summary$percentage, 2), "%")

# Create a comma-separated string of entity information
entities_info <- paste(entity_info, collapse=", ")

# Create a comma-separated string of donation sums
donations <- paste(summary$sumvalue, collapse=", ")

# Combine all information into one summary string
output <- paste0("There are ", n_entities, " entities that donated more than 5%, and they are: ", entities_info)

output
```

```
## [1] "There are 2 entities that donated more than 5%, and they are: Labour donated 8.82 million(s), representing 6.91%, Liberal Democrats donated 8 million(s), representing 6.27%"
```

## Do `entity` donors give to a single party or not?

-   How many distinct entities who paid money to MPS are there?


```r
# SQL query to get the count of distinct entities who paid money to MPs
Distinct_Entities_Count <- dbGetQuery(con, "
SELECT COUNT(DISTINCT p.entity) AS distinct_entities
FROM payments p
JOIN members m ON p.member_id = m.id
")

# Retrieve the count of distinct entities
distinct_entities <- Distinct_Entities_Count$distinct_entities

# Combine the count into a readable string
output <- paste0("There are ", distinct_entities, " distinct entities who paid money to MPs.")

output
```

```
## [1] "There are 2213 distinct entities who paid money to MPs."
```


```r
# Join 'payments' and 'members' data frames using 'member_id' and 'id' columns
joined_data <- inner_join(payments, members, by = c("member_id" = "id"))

# Select distinct entities from the joined data
distinct_entities <- distinct(joined_data, entity)

# Calculate the count of distinct entities
entity_count <- nrow(distinct_entities)

# Combine the count into a readable string
output <- paste0("There are ", entity_count, " distinct entities who paid money to MPs.")

output
```

```
## [1] "There are 2213 distinct entities who paid money to MPs."
```

-   How many (as a number and %) donated to MPs belonging to a single party only?


```r
# Join the 'payments' and 'members' data frames based on the matching values of the 'member_id' column
joined_data <- inner_join(payments, members, by = c("member_id" = "id"))

# Group the data by entity and party_id, and count the distinct entities within each party
party_entity_counts <- joined_data %>%
  group_by(entity) %>%
  summarise(distinct_parties = n_distinct(party_id))

# Filter entities that donated to MPs belonging to a single party only
single_party_entities <- party_entity_counts %>%
  filter(distinct_parties == 1)

# Count the number of entities and calculate the percentage
total_entities <- nrow(party_entity_counts)
single_party_count <- nrow(single_party_entities)
percentage_single_party <- (single_party_count / total_entities) * 100

# Print the results
cat("Number of entities that donated to MPs belonging to a single party only:", single_party_count, "\n")
```

```
## Number of entities that donated to MPs belonging to a single party only: 2036
```

```r
cat("Percentage of entities that donated to MPs belonging to a single party only:", percentage_single_party, "%\n")
```

```
## Percentage of entities that donated to MPs belonging to a single party only: 92 %
```

## Which party has raised the greatest amount of money in each of the years 2020-2022?


```r
# Join the 'payments' and 'members' data frames based on the matching values of the 'member_id' column
joined_data <- inner_join(payments, members, by = c("member_id" = "id"))

# Group the data by party_id and calculate the total amount raised by each party
party_amounts <- joined_data %>%
  group_by(party_id) %>%
  summarise(total_amount = sum(value))

# Find the party with the maximum total amount
party_max_amount <- party_amounts %>%
  filter(total_amount == max(total_amount))

# Print the result
cat("The party that raised the greatest amount of money had party_id:", party_max_amount$party_id, "with a total of:", party_max_amount$total_amount, "\n")
```

```
## The party that raised the greatest amount of money had party_id: p4 with a total of: 2.5e+07
```



```r
# Join the 'party_donations' and 'parties' tables based on the matching values of the 'party_id' column
joined_data <- inner_join(party_donations, parties, by = c("party_id" = "id"))

# Convert the 'date' column to a Date object
joined_data$date <- as.Date(joined_data$date)

# Extract the year from the 'date' column
joined_data$year <- year(joined_data$date)

# Filter data from the year 2020 onwards
joined_data <- joined_data %>%
  filter(year >= 2020)

# Group the data by year, party_id, and party_name, and calculate the total amount raised by each party in each year
party_year_amounts <- joined_data %>%
  group_by(year, name) %>%
  summarise(total_year_donations = sum(value), .groups = "drop")

# Calculate the total donations for each year, and drop the grouping
total_year_donations <- party_year_amounts %>%
  group_by(year) %>%
  summarise(total_donations = sum(total_year_donations), .groups = "drop")

# Calculate the proportion of each party's donations for each year, and drop the grouping
party_year_amounts <- party_year_amounts %>%
  left_join(total_year_donations, by = "year") %>%
  mutate(prop = total_year_donations / total_donations) %>%
  ungroup()  # ungroup() is equivalent to summarise(.groups = "drop")

# Show only the first five rows, excluding the "total_donations" column
head(party_year_amounts[, -which(names(party_year_amounts) == "total_donations")], 5)
```

```
## # A tibble: 5 × 4
##    year name              total_year_donations    prop
##   <dbl> <chr>                            <dbl>   <dbl>
## 1  2020 Alliance                       105000  0.00150
## 2  2020 Conservative                 42770782. 0.612  
## 3  2020 Green Party                    378068  0.00541
## 4  2020 Labour                       13539803. 0.194  
## 5  2020 Liberal Democrats            12717295. 0.182
```

... and then, based on this data, plot the following graph.

<img src="../../images/total_donations_graph.png" width="80%" style="display: block; margin: auto;" />


```r
# Define the official colors for each party
official_colors <- c("Alliance" = "#ED1C24", "Conservative" = "#0087DC", "Green Party" = "#6AB023",
                     "Labour" = "#DC241F", "Liberal Democrats" = "#FDBB30", "Plaid Cymru" = "#FF7900",
                     "Scottish National Party" = "#FFDD00", "Sinn Féin" = "#800080", "Alba Party" = "#773344",
                     "Democratic Unionist Party" = "#D46A4C")

# Create a function to format the labels
format_labels <- function(x) {
  paste0(x/1e6, "M")
}

# Reorder the party names within each year based on total donations in descending order
party_year_amounts_1 <- party_year_amounts %>%
  mutate(name = fct_reorder(name, total_year_donations, .desc = TRUE)) %>%
  arrange(year, desc(total_year_donations))

# Create the bar chart
ggplot(data = party_year_amounts_1, aes(x = factor(year), y = total_year_donations, fill = name)) +
  geom_bar(stat = "identity", position = "dodge") +
  labs(title = "Donations concentrated in top two parties", subtitle = "Total Donations by Year per Party", x = "Year", y = "Total Year Donations") +
  scale_fill_manual(values = official_colors, name = "Party") +
  theme_minimal() +
  scale_y_continuous(labels = scales::comma_format(scale = 1e-6, accuracy = 0.1, suffix = "M"), expand = c(0, 0)) +
  theme(axis.text.y = element_text(size = 8))
```

<img src="/blogs/blo5_files/figure-html/unnamed-chunk-10-1.png" width="648" style="display: block; margin: auto;" />


```r
#Graph with 3 main parties and the others group in others

# Determine the top 3 parties by total donations
top_parties <- party_year_amounts %>%
  group_by(name) %>%
  summarise(total_donations = sum(total_year_donations)) %>%
  arrange(desc(total_donations)) %>%
  head(3) %>%
  pull(name)

# Convert top_parties to character vector
top_parties <- as.character(top_parties)

# Group the remaining parties as "Others"
party_year_amounts <- party_year_amounts %>%
  mutate(name = fct_other(name, keep = top_parties, other_level = "Others"))

# Reorder the party names within each year based on total donations in descending order
party_year_amounts_2 <- party_year_amounts %>%
  mutate(name = fct_reorder(name, total_year_donations, .desc = TRUE)) %>%
  arrange(year, desc(total_year_donations))

# Create the bar chart
ggplot(data = party_year_amounts_2, aes(x = factor(year), y = total_year_donations, fill = name)) +
  geom_bar(stat = "identity", position = "dodge") +
  labs(title = "Conservative - Top Donor but Declining Yearly", 
       subtitle = "Total Donations by Year per Party",
       x = "Year", 
       y = "Total Year Donations") +
  scale_fill_manual(values = c(official_colors[top_parties], Others = "#CCCCCC"), name = "Party") +
  theme_minimal() +
  scale_y_continuous(labels = scales::comma_format(scale = 1e-6, accuracy = 0.1, suffix = "M"), expand = c(0, 0)) +
  theme(axis.text.y = element_text(size = 8))
```

<img src="/blogs/blo5_files/figure-html/unnamed-chunk-11-1.png" width="648" style="display: block; margin: auto;" />


This uses the default ggplot colour pallete, as I dont want you to worry about using the [official colours for each party](https://en.wikipedia.org/wiki/Wikipedia:Index_of_United_Kingdom_political_parties_meta_attributes). However, I would like you to ensure the parties are sorted according to total donations and not alphabetically. You may even want to remove some of the smaller parties that hardly register on the graph. Would facetting help you?

Finally, when you are done working with the databse, make sure you close the connection, or disconnect from the database.


```r
dbDisconnect(con)
```
