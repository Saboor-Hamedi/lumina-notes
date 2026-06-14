---
tags: [r, tidymodels, caret, modeling, machine-learning]
---

# 07 — Modeling

## tidymodels

```r
library(tidymodels)

# Data splitting
set.seed(42)
split <- initial_split(df, prop = 0.8, strata = outcome)
train <- training(split)
test <- testing(split)

# Cross-validation
folds <- vfold_cv(train, v = 5, repeats = 3, strata = outcome)

# Recipe for preprocessing
recipe <- recipe(outcome ~ ., data = train) %>%
    step_impute_knn(all_numeric_predictors(), neighbors = 5) %>%
    step_impute_mode(all_nominal_predictors()) %>%
    step_normalize(all_numeric_predictors()) %>%
    step_dummy(all_nominal_predictors(), one_hot = TRUE) %>%
    step_corr(all_numeric_predictors(), threshold = 0.9)

# Model specification
rf_spec <- rand_forest(trees = 1000, mtry = tune(), min_n = tune()) %>%
    set_engine("ranger", importance = "permutation") %>%
    set_mode("classification")

# Workflow
workflow <- workflow() %>%
    add_recipe(recipe) %>%
    add_model(rf_spec)

# Tuning grid
grid <- grid_latin_hypercube(
    finalize(mtry(), train %>% select(-outcome) %>% ncol()),
    min_n(), size = 20
)

# Tune
tune_results <- tune_grid(
    workflow, resamples = folds, grid = grid,
    metrics = metric_set(accuracy, roc_auc, f_meas)
)

# Select best
best <- select_best(tune_results, metric = "roc_auc")
final_wf <- finalize_workflow(workflow, best)
final_fit <- last_fit(final_wf, split)

# Evaluate
collect_metrics(final_fit)
collect_predictions(final_fit) %>%
    roc_curve(outcome, .pred_class) %>%
    autoplot()

# Feature importance
library(vip)
final_fit %>%
    extract_fit_engine() %>%
    vip(num_features = 20, geom = "point")
```

## caret

```r
library(caret)

# Partition
set.seed(42)
train_index <- createDataPartition(df$outcome, p = 0.8, list = FALSE)
train <- df[train_index, ]
test <- df[-train_index, ]

# Training control
ctrl <- trainControl(
    method = "repeatedcv", number = 10, repeats = 3,
    verboseIter = TRUE, classProbs = TRUE,
    summaryFunction = twoClassSummary
)

# Grid search
grid <- expand.grid(
    mtry = c(2, 4, 6, 8, 10),
    splitrule = c("gini", "extratrees"),
    min.node.size = c(1, 5, 10)
)

# Train
model <- train(
    outcome ~ ., data = train,
    method = "ranger", trControl = ctrl,
    tuneGrid = grid, metric = "ROC",
    num.trees = 500, importance = "permutation"
)

# Predict & Evaluate
preds <- predict(model, test)
probs <- predict(model, test, type = "prob")
confusionMatrix(preds, test$outcome, positive = "Class1")

# Variable importance
imp <- varImp(model)
plot(imp, top = 15)
```

**Links**: [[Web-Dev/Programming/R for Data Science/06 Statistical Tests]] | [[Web-Dev/Programming/R for Data Science/04 Data Tidying (tidyr)]] | [[Web-Dev/Programming/R for Data Science/09 Performance (data.table & purrr)]]
**See also**: [[scikit-learn/_MOC]], [[Machine Learning Fundamentals]]
