---
tags: [r, dplyr, data-wrangling]
---

# 03 — Data Wrangling (dplyr)

## Window Functions

```r
library(dplyr)

df %>%
    group_by(category) %>%
    mutate(
        row_num = row_number(),
        rank_desc = row_number(desc(amount)),
        dense_rank = dense_rank(desc(amount)),
        quartile = ntile(amount, 4),
        percentile = percent_rank(amount) * 100,
        cume_dist = cume_dist(amount)
    ) %>%
    ungroup()

# Lag and lead
df %>%
    group_by(customer_id) %>%
    arrange(date) %>%
    mutate(
        prev_amount = lag(amount, 1),
        amount_change = amount - lag(amount),
        pct_change = (amount - lag(amount)) / lag(amount) * 100
    ) %>%
    ungroup()

# Cumulative functions
df %>%
    group_by(customer_id) %>%
    arrange(date) %>%
    mutate(
        total_to_date = cumsum(amount),
        running_avg = cummean(amount),
        running_max = cummax(amount)
    ) %>%
    ungroup()
```

## rowwise() Operations

```r
df <- df %>%
    rowwise() %>%
    mutate(
        avg_score = mean(c(score1, score2, score3, score4), na.rm = TRUE),
        max_score = max(c(score1, score2, score3, score4), na.rm = TRUE),
        min_score = min(c(score1, score2, score3, score4), na.rm = TRUE)
    ) %>%
    ungroup()

# Using c_across for dynamic selection
df <- df %>%
    rowwise() %>%
    mutate(
        total = sum(c_across(starts_with("item_")), na.rm = TRUE),
        n_nonmissing = sum(!is.na(c_across(starts_with("item_")))),
        avg = total / n_nonmissing
    ) %>%
    ungroup()
```

## across() — Apply Functions Over Columns

```r
# Summarize all numeric columns
df %>%
    summarise(across(where(is.numeric),
        list(mean = ~ mean(.x, na.rm = TRUE),
             sd = ~ sd(.x, na.rm = TRUE),
             median = ~ median(.x, na.rm = TRUE))))

# Transform multiple columns
df <- df %>%
    mutate(across(c(age, income, experience),
        ~ as.numeric(scale(.x)),
        .names = "{.col}_z"))

# Conditional across
df <- df %>%
    mutate(across(where(is.character) & !c(id, name),
        ~ if_else(is.na(.x), "Unknown", .x)))

# Grouped across
df %>%
    group_by(region) %>%
    summarise(across(c(amount, fee, discount),
        list(mean = ~ mean(.x, na.rm = TRUE),
             total = ~ sum(.x, na.rm = TRUE)),
        .names = "{.col}_{.fn}"))

# Filter with across
df %>%
    filter(across(contains("score"), ~ .x >= 70))
```

## Grouped Operations

```r
df %>%
    group_by(department) %>%
    filter(amount == max(amount, na.rm = TRUE))  # Top per group

df %>%
    group_by(category) %>%
    slice_max(amount, n = 3)  # Top 3 per group

df %>%
    group_by(customer) %>%
    filter(n() >= 5)  # Customers with 5+ transactions

# Proportion of group
df %>%
    group_by(region) %>%
    mutate(region_total = sum(amount, na.rm = TRUE)) %>%
    ungroup() %>%
    mutate(pct_of_region = amount / region_total * 100)
```

**Links**: [[Web-Dev/Programming/R for Data Science/04 Data Tidying (tidyr)]] | [[Web-Dev/Programming/R for Data Science/02 Data Import]] | [[Web-Dev/Programming/R for Data Science/05 Visualization (ggplot2)]]
**See also**: [[Pandas for Data Analysis]]
