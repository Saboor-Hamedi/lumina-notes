---
id: ds-viz-0002-0000-0000-000000000002
title: Seaborn Guide
language: markdown
tags: [seaborn, visualization, statistics, plotting]
selection: null
isPinned: false
timestamp: 1781700000702
---
# Seaborn Guide

**Links**: [[_MOC]] | [[Matplotlib]] | [[Plotly]]

## Setup

```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.set_theme()                           # Default seaborn style
sns.set_theme(style='darkgrid')           # Options: darkgrid, whitegrid, dark, white, ticks
sns.set_palette('viridis')                # Set color palette
sns.set_context('paper')                  # paper, notebook, talk, poster
```

## Common Plot Types

```python
# Load built-in dataset
tips = sns.load_dataset('tips')

# Scatter plot with trend line
sns.lmplot(data=tips, x='total_bill', y='tip', hue='time')

# Box plot
sns.boxplot(data=tips, x='day', y='total_bill', hue='sex')

# Violin plot (distribution + box)
sns.violinplot(data=tips, x='day', y='total_bill', hue='sex', split=True)

# Bar plot with error bars
sns.barplot(data=tips, x='day', y='total_bill', hue='sex')

# Point plot (trend across categories)
sns.pointplot(data=tips, x='day', y='total_bill', hue='sex')
```

## Distribution Plots

```python
# Histogram + KDE
sns.histplot(data=tips, x='total_bill', bins=30, kde=True)
sns.kdeplot(data=tips, x='total_bill', hue='time', fill=True)
sns.ecdfplot(data=tips, x='total_bill')          # Empirical CDF

# Joint distribution
sns.jointplot(data=tips, x='total_bill', y='tip', kind='hex')
sns.jointplot(data=tips, x='total_bill', y='tip', kind='kde')

# Pairwise relationships
sns.pairplot(data=tips, hue='sex', diag_kind='kde')
```

## Categorical Plots

```python
# Strip plot (one-dimensional scatter)
sns.stripplot(data=tips, x='day', y='total_bill', jitter=True)

# Swarm plot (non-overlapping strip)
sns.swarmplot(data=tips, x='day', y='total_bill', hue='sex', dodge=True)

# Cat plot (relational, categorical)
sns.catplot(data=tips, x='day', y='total_bill', hue='sex',
    col='time', kind='box')
```

## Heatmaps

```python
# Correlation heatmap
corr = tips.corr(numeric_only=True)
sns.heatmap(corr, annot=True, cmap='coolwarm', center=0,
    square=True, linewidths=0.5)

# Confusion matrix
sns.heatmap(confusion_matrix, annot=True, fmt='d', cmap='Blues')

# Clustered heatmap
sns.clustermap(corr, annot=True, cmap='coolwarm', center=0)
```

## Themes and Aesthetics

```python
# Set global style
sns.set_theme(
    style='whitegrid',
    palette='Set2',
    font='sans-serif',
    font_scale=1.2,
    rc={'figure.figsize': (10, 6)}
)

# Remove top/right spines
sns.despine()
sns.despine(left=True, bottom=True)

# Color palettes
sns.color_palette('viridis', n_colors=5)
sns.color_palette('Set2')
sns.color_palette('husl', n_colors=8)
sns.color_palette('Paired')
sns.diverging_palette(240, 10, n=9)       # Custom diverging
sns.dark_palette('purple', n_colors=6)
sns.light_palette('blue', reverse=True)
```

## Why Seaborn Over Matplotlib

```python
# Matplotlib: 15 lines to get a nice boxplot with hue
# Seaborn: 1 line
sns.boxplot(data=df, x='category', y='value', hue='group')

# Matplotlib: manual stats computation for trend line
# Seaborn: automatic
sns.regplot(data=df, x='x', y='y', logistic=False)

# Matplotlib: manual pair grid
# Seaborn: one-liner
sns.pairplot(df, hue='target', diag_kind='kde')
```
