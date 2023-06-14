---
categories:  
- ""    #the front matter should be like the one found in, e.g., blog2.md. It cannot be like the normal Rmd we used
- ""
date: "2021-09-30"
description: The Data Harvesters Unearthing Hidden Treasures through Elegant Web Scraping # the title that will show up once someone gets to this page
draft: false
image: scra2.jpg # save picture in \static\img\blogs. Acceptable formats= jpg, jpeg, or png . Your iPhone pics wont work

keywords: ""
slug: blo6 # slug is the shorthand URL address... no spaces plz
title: Scraping 
---
  









# Scraping consulting jobs

The website [https://www.consultancy.uk/jobs/](https://www.consultancy.uk/jobs) lists job openings for consulting jobs.


```r
library(robotstxt)
paths_allowed("https://www.consultancy.uk") #is it ok to scrape?

base_url <- "https://www.consultancy.uk/jobs/page/1"

listings_html <- base_url %>%
  read_html()
```

Identify the CSS selectors in order to extract the relevant information from this page, namely

1.  job
2.  firm
3.  functional area
4.  type


```r
#1.  job elements: tr.active
#2.  firm: td.hide-phone
#3.  td.hide-tablet-and-less
#4.  td.hide-tablet-landscape
```


Can you get all pages of ads, and not just the first one, `https://www.consultancy.uk/jobs/page/1` into a dataframe?

-   Write a function called `scrape_jobs()` that scrapes information from the webpage for consulting positions. This function should

    -   have one input: the URL of the webpage and should return a data frame with four columns (variables): job, firm, functional area, and type

    -   Test your function works with other pages too, e.g., <https://www.consultancy.uk/jobs/page/2>. Does the function seem to do what you expected it to do?

    -   Given that you have to scrape `...jobs/page/1`, `...jobs/page/2`, etc., define your URL so you can join multiple stings into one string, using `str_c()`. For instnace, if `page` is 5, what do you expect the following code to produce?

```         
base_url <- "https://www.consultancy.uk/jobs/page/1"
url <- str_c(base_url, page)
```


```r
scrape_jobs <- function(base_url, num_pages) {
  jobs_data <- data.frame(job = character(),
                          firm = character(),
                          functional_area = character(),
                          type = character(),
                          stringsAsFactors = FALSE)

  safe_html_text <- possibly(.f = html_text, otherwise = NA_character_)

  for (page in 1:num_pages) {
    url <- str_c(base_url, page)
    page_html <- read_html(url)

    job_elements <- page_html %>%
      html_nodes("tr.active")

    job_titles <- job_elements %>%
      html_node(".title") %>%
      safe_html_text() %>%
      trimws()

    firms <- job_elements %>%
      html_node("td.hide-phone") %>%
      safe_html_text() %>%
      trimws()

    functional_areas <- job_elements %>%
      html_node("td.hide-tablet-and-less") %>%
      safe_html_text() %>%
      trimws() %>%
      purrr::map_chr(~str_split(.x, "\n", simplify = TRUE)[1, 1]) #I need only the first element

    types <- job_elements %>%
      html_node("td.hide-tablet-landscape") %>%
      safe_html_text() %>%
      trimws()

    page_data <- data.frame(job = job_titles,
                            firm = firms,
                            functional_area = functional_areas,
                            type = types,
                            stringsAsFactors = FALSE)

    jobs_data <- rbind(jobs_data, page_data)
  }

  return(jobs_data)
}

# Usage - here I define the url and the number of pages (max 8 due to the page)
base_url <- "https://www.consultancy.uk/jobs/page/"
num_pages <- 5

jobs_df <- scrape_jobs(base_url, num_pages)
```

-   Construct a vector called `pages` that contains the numbers for each page available


```r
# Define the range of pages
start_page <- 1
end_page <- 8

# Initialize an empty vector to store the URLs
urls <- c()

# Iterate over each page and construct the URL
for (page in start_page:end_page) {
  url <- paste0("https://www.consultancy.uk/jobs/page/", page)
  urls <- c(urls, url)
}

# Print the vector of URLs
print(urls)
```

```
## [1] "https://www.consultancy.uk/jobs/page/1"
## [2] "https://www.consultancy.uk/jobs/page/2"
## [3] "https://www.consultancy.uk/jobs/page/3"
## [4] "https://www.consultancy.uk/jobs/page/4"
## [5] "https://www.consultancy.uk/jobs/page/5"
## [6] "https://www.consultancy.uk/jobs/page/6"
## [7] "https://www.consultancy.uk/jobs/page/7"
## [8] "https://www.consultancy.uk/jobs/page/8"
```


-   Map the `scrape_jobs()` function over `pages` in a way that will result in a data frame called `all_consulting_jobs`.


```r
# Define the base URL and range of pages
base_url <- "https://www.consultancy.uk/jobs/page/"
start_page <- 1
end_page <- 8

# Initialize an empty data frame to store all consulting jobs
all_consulting_jobs <- data.frame()

# Iterate over each page and append the scraped data to all_consulting_jobs
for (page in start_page:end_page) {
  page_jobs <- scrape_jobs(base_url, page)
  all_consulting_jobs <- rbind(all_consulting_jobs, page_jobs)
}

# Show only the first five rows
head(all_consulting_jobs, 5)
```

```
##                                             job                       firm
## 1                         Healthcare consultant         Develop Consulting
## 2                                Senior Analyst CIL Management Consultants
## 3                                   Internships               Simon-Kucher
## 4 Business Development Manager – R&D Incentives                     Ayming
## 5                     Senior 3D/Motion Designer          Yonder Consulting
##   functional_area       type
## 1 Lean & SixSigma        Job
## 2        Strategy        Job
## 3         Pricing Internship
## 4           Sales        Job
## 5       Marketing        Job
```


-   Write the data frame to a csv file called `all_consulting_jobs.csv` in the `data` folder.


```r
# Ensure the 'data' directory exists
if (!dir.exists("data")) {
  dir.create("data")
}

# Write the data frame to a csv file
write.csv(all_consulting_jobs, file = "data/all_consulting_jobs.csv", row.names = FALSE)
```
