---
tags: [r, data-table, purrr, performance]
---

# 09 — Performance (data.table & purrr)

## data.table

```r
library(data.table)

# Create or convert
dt <- data.table(
    id = 1:1e6,
    group = sample(LETTERS[1:20], 1e6, replace = TRUE),
    category = sample(c("A", "B", "C"), 1e6, replace = TRUE),
    value = rnorm(1e6),
    date = sample(seq.Date(as.Date("2020-01-01"), as.Date("2024-12-31"), by = "day"), 1e6, TRUE)
)

# Fast filtering
dt[group == "A" & value > 2]
dt[category %in% c("A", "B")]

# Fast aggregations
dt[, .(avg = mean(value), n = .N), by = group]
dt[, .(avg = mean(value)), by = .(group, category)]
dt[, lapply(.SD, mean), by = group, .SDcols = is.numeric]

# Keys and joins
setkey(dt, id)
setkey(dt2, id)
merged <- dt[dt2, nomatch = 0]  # Inner join
merged <- dt[dt2]               # Left join

# Rolling joins (for time series)
setkey(dt, id, date)
setkey(dt2, id, date)
dt[dt2, roll = TRUE]   # Last observation carried forward
dt[dt2, roll = -1]      # Next observation carried backward

# Chaining
result <- dt[value > 0][
    , .(total = sum(value), .N), by = .(group, category)
][order(-total)]

# fread — fast file reading
big <- fread("large.csv",
    nrows = 1e6,
    select = c("id", "value", "date"),
    colClasses = list(numeric = c("value", "amount")),
    na.strings = c("", "NA"),
    nThread = 4)

# fwrite — fast writing
fwrite(dt, "output.csv", sep = ",", nThread = 4, dateTimeAs = "ISO", bom = TRUE)

# In-place modification (no copy)
set(dt, i = which(dt$value < 0), j = "value", value = NA)
setnames(dt, old = "value", new = "amount")
setcolorder(dt, c("id", "date", "amount", "group"))
```

## purrr — Iteration

```r
library(purrr)

# map basics
map(1:5, ~ .x ^ 2)
map_dbl(1:5, ~ .x ^ 2)
map_chr(iris, class)

# map on data frames
df %>% map_dbl(~ sum(is.na(.x)))
df %>% map_df(~ summary(.x))

# map with multiple inputs
map2(x, y, ~ .x + .y)
map2(df1, df2, ~ bind_rows(.x, .y))
pmap(list(a, b, c, d), ~ sum(...))

# Iterate safely
safe_log <- safely(log)
results <- map(c(1, -1, 0, 5), safe_log)
transposed <- transpose(results)
transposed$result
transposed$error

# Possibly (return NA instead of error)
map_dbl(c(1, -1, 0, "a"), possibly(log, NA_real_))

# Walk (side effects only)
walk(files, ~ cat(readLines(.x), sep = "\n"))

# Nested data frames
by_group <- df %>%
    group_by(category) %>%
    nest() %>%
    mutate(
        model = map(data, ~ lm(amount ~ date, data = .x)),
        tidy = map(model, broom::tidy),
        glance = map(model, broom::glance),
        r_squared = map_dbl(glance, "r.squared")
    )

by_group %>%
    arrange(desc(r_squared)) %>%
    unnest(tidy)

# Keep and discard
keep(df, is.numeric)
discard(df, is.numeric)

# Compact (remove NULL)
compact(list(a = 1, b = NULL, c = 3))
```

**Links**: [[Web-Dev/Programming/R for Data Science/03 Data Wrangling (dplyr)]] | [[Web-Dev/Programming/R for Data Science/07 Modeling]] | [[Web-Dev/Programming/R for Data Science/10 Shiny & Tables]]
**See also**: [[Functional Programming Concepts]], [[Big Data Processing]]
