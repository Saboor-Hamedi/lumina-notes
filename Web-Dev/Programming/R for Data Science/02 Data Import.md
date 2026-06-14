---
tags: [r, data-import, readr, readxl, haven, dbi]
---

# 02 — Data Import

## readr — Flat Files

```r
library(readr)

df <- read_csv("data.csv",
    col_types = cols(
        date = col_date("%Y-%m-%d"),
        amount = col_double(),
        category = col_factor(),
        id = col_character()
    ),
    na = c("", "NA", "NULL"),
    skip = 0,
    n_max = Inf
)

# Delimited files
df <- read_delim("data.txt", delim = "|", quote = "\"")

# Write
write_csv(df, "output.csv", na = "")
write_tsv(df, "output.tsv")
write_rds(df, "compressed.rds", compress = "bz2")
```

## readxl — Excel Files

```r
library(readxl)

excel_sheets("report.xlsx")

df <- read_excel("report.xlsx", sheet = "Sheet1")
df <- read_excel("data.xlsx",
    range = "A1:G100",
    col_types = c("text", "date", "numeric", "numeric", "text", "text", "numeric"),
    na = "NA"
)

# Read all sheets
all_sheets <- excel_sheets("data.xlsx") %>%
    set_names() %>%
    map(~ read_excel("data.xlsx", sheet = .x))
```

## haven — SPSS, Stata, SAS

```r
library(haven)

# SPSS
df <- read_sav("survey.sav")
df <- read_por("survey.por")

# Stata
df <- read_dta("data.dta")
attr(df$variable, "label")

# SAS
df <- read_sas("data.sas7bdat")
df <- read_xpt("data.xpt")

# Write
write_dta(df, "export.dta", version = 14)
write_sav(df, "export.sav")
```

## DBI — Database Interface

```r
library(DBI)

# Connect
con <- dbConnect(RPostgres::Postgres(),
    host = "localhost", port = 5432,
    dbname = "analytics", user = "admin",
    password = Sys.getenv("PG_PASSWORD"))

con_sqlite <- dbConnect(RSQLite::SQLite(), "local.db")

con_bigquery <- dbConnect(bigrquery::bigquery(),
    project = "my-project", dataset = "analytics")

# Read
df <- dbGetQuery(con, "SELECT * FROM orders WHERE date >= $1",
    params = list("2024-01-01"))

# Write
dbWriteTable(con, "clean_orders", df, overwrite = TRUE)
dbAppendTable(con, "orders", batch_df)

dbDisconnect(con)
```

**Links**: [[Web-Dev/Programming/R for Data Science/01 Getting Started]] | [[Web-Dev/Programming/R for Data Science/03 Data Wrangling (dplyr)]] | [[Web-Dev/Programming/R for Data Science/04 Data Tidying (tidyr)]]
**See also**: [[Data Engineering]], [[Pandas for Data Analysis]]
