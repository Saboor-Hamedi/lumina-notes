---
tags: [r, statistics, hypothesis-testing]
---

# 06 — Statistical Tests

```r
set.seed(42)
group1 <- rnorm(30, mean = 100, sd = 15)
group2 <- rnorm(30, mean = 115, sd = 15)
before <- rnorm(20, mean = 80, sd = 10)
after <- before + rnorm(20, mean = 5, sd = 3)
```

## t-Tests

```r
# Independent
t.test(group1, group2, var.equal = TRUE)   # Student's
t.test(group1, group2, var.equal = FALSE)  # Welch's (default)
t.test(group1, group2, alternative = "greater")

# Paired
t.test(before, after, paired = TRUE)

# One sample
t.test(scores, mu = 75)

# Effect size
library(effsize)
cohen.d(group1, group2)
```

## Chi-Square and Fisher

```r
tbl <- table(df$treatment, df$outcome)
chisq.test(tbl)
fisher.test(tbl)  # For small expected counts
```

## ANOVA

```r
model_aov <- aov(score ~ treatment + block, data = df)
summary(model_aov)
TukeyHSD(model_aov)  # Post-hoc pairwise comparisons

# MANOVA
model_manova <- manova(cbind(score1, score2) ~ treatment, data = df)
summary(model_manova, test = "Wilks")
```

## Correlation

```r
cor(df$age, df$income, method = "pearson")
cor.test(df$age, df$income, method = "spearman", exact = FALSE)

# Correlation matrix
library(corrplot)
num_df <- df %>% select(where(is.numeric))
cor_mat <- cor(num_df, use = "pairwise.complete.obs")
corrplot(cor_mat, method = "color", type = "upper",
    order = "hclust", tl.col = "black", tl.srt = 45,
    addCoef.col = "black", number.cex = 0.7)
```

**Links**: [[Web-Dev/Programming/R for Data Science/07 Modeling]] | [[Web-Dev/Programming/R for Data Science/05 Visualization (ggplot2)]] | [[Web-Dev/Programming/R for Data Science/01 Getting Started]]
**See also**: [[Hypothesis Testing]], [[A B Testing]]
