---
tags: [r, r-markdown, quarto, reports]
---

# 08 — Reports (R Markdown & Quarto)

## Parameterized Reports

```markdown
---
title: "`r params$region` Sales Report"
params:
  region: "All"
  year: 2024
  threshold: 100
  output_type: "summary"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = FALSE, warning = FALSE, message = FALSE)
library(tidyverse)
```

```{r data}
data <- read_csv("sales.csv") %>%
    filter(
        if (params$region != "All") region == params$region else TRUE,
        year == params$year,
        amount > params$threshold
    )
```

Total Revenue: `r scales::dollar(sum(data$amount))`
Transactions: `r nrow(data)`

```{r plot}
ggplot(data, aes(x = date, y = amount, color = category)) +
    geom_line() +
    labs(title = paste(params$region, "Daily Sales"))
```
```

## Caching and Performance

```markdown
```{r large-model, cache=TRUE}
# Only re-runs on code change
model <- ranger(outcome ~ ., data = train, num.trees = 2000)
```

```{r dependent, cache=TRUE, dependson="large-model"}
preds <- predict(model, test)
```

```{r, cache=TRUE, cache.extra=params$year}
aggregated <- data %>%
    filter(year == params$year) %>%
    group_by(region) %>%
    summarise(total = sum(amount))
```
```

## Quarto Documents

```markdown
---
title: "Analysis Report"
author: "Data Team"
format:
  html:
    theme: cosmo
    code-fold: true
    toc: true
    toc-depth: 3
  pdf:
    documentclass: article
    papersize: letter
  docx: default
execute:
  cache: true
  warning: false
  message: false
---

## Results

```{r}
#| label: fig-results
#| fig-cap: "Main Results"
#| fig-width: 8
#| fig-height: 5
#| column: page-right
ggplot(data, aes(x = date, y = metric)) +
    geom_smooth() +
    theme_minimal()
```

```{r}
#| tbl-cap: "Summary Statistics"
data %>%
    group_by(category) %>%
    summarise(across(where(is.numeric), mean, na.rm = TRUE)) %>%
    gt()
```
```

**Links**: [[Web-Dev/Programming/R for Data Science/05 Visualization (ggplot2)]] | [[Web-Dev/Programming/R for Data Science/10 Shiny & Tables]] | [[Web-Dev/Programming/R for Data Science/11 Quick Reference]]
**See also**: [[Technical Writing]], [[Documentation]]
