---
tags: [r, tidyr, data-tidying]
---

# 04 — Data Tidying (tidyr)

## pivot_longer and pivot_wider

```r
library(tidyr)

# Wide to Long
long <- df %>%
    pivot_longer(
        cols = c(q1, q2, q3, q4, q5),
        names_to = "question",
        values_to = "score",
        names_prefix = "q",
        values_drop_na = TRUE
    )

# Multiple value columns
long <- df %>%
    pivot_longer(
        cols = -c(id, name),
        names_to = c(".value", "year"),
        names_sep = "_"
    )

# Long to Wide
wide <- df %>%
    pivot_wider(
        id_cols = customer_id,
        names_from = category,
        values_from = amount,
        values_fill = list(amount = 0),
        values_fn = list(amount = sum)
    )
```

## separate, unite, extract

```r
# Split column
df <- df %>%
    separate(name, into = c("last", "first"), sep = ", ")

df <- df %>%
    separate(date, into = c("year", "month", "day"), sep = "-", convert = TRUE)

# Combine columns
df <- df %>%
    unite("full_address", street, city, state, zip, sep = ", ", na.rm = TRUE)

# Regex extraction
df <- df %>%
    extract(email, into = "domain", regex = "@(.+)$")
```

## Nest and Unnest

```r
# Nest data frames within a data frame
nested <- df %>%
    group_by(region) %>%
    nest()

nested$data[[1]]

# Unnest back
df_restored <- nested %>%
    unnest(data)

# Nest for modeling
by_region <- df %>%
    group_by(region) %>%
    nest() %>%
    mutate(
        model = map(data, ~ lm(value ~ time, data = .x)),
        tidied = map(model, broom::tidy),
        glanced = map(model, broom::glance)
    )

by_region %>%
    unnest(tidied)

# Unnest wider
by_region %>%
    unnest_wider(glanced)
```

**Links**: [[Web-Dev/Programming/R for Data Science/03 Data Wrangling (dplyr)]] | [[Web-Dev/Programming/R for Data Science/05 Visualization (ggplot2)]] | [[Web-Dev/Programming/R for Data Science/07 Modeling]]
**See also**: [[Pandas for Data Analysis]], [[Data Engineering]]
