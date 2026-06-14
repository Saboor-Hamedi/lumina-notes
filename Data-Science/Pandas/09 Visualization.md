---
id: ds-pd-0009-0000-0000-000000000009
title: Pandas Visualization
language: markdown
tags: [pandas, visualization, matplotlib, plotting]
selection: null
isPinned: false
timestamp: 1781700000509
---
# Pandas Visualization

**Links**: [[_MOC]] | [[05 GroupBy Aggregation]] | [[08 Time Series]]

## .plot API

```python
import matplotlib.pyplot as plt
import numpy as np

# Line plot
df.set_index('date')['value'].plot(
    title='Time Series',
    figsize=(12, 6),
    grid=True,
    linestyle='-',
    linewidth=2,
)

# Bar plot
df.groupby('category')['amount'].sum().plot(
    kind='bar',
    title='Revenue by Category',
    color='steelblue',
    edgecolor='black',
)
plt.xticks(rotation=45)

# Horizontal bar
df.groupby('region')['amount'].mean().plot(kind='barh')

# Histogram
df['age'].plot(kind='hist', bins=30, alpha=0.7,
    title='Age Distribution', edgecolor='black')

# Box plot
df[['age', 'income', 'experience']].plot(kind='box',
    title='Distribution Summary')

# Area plot
df.groupby('date')['amount'].sum().plot(kind='area', alpha=0.5)

# Scatter plot
df.plot(kind='scatter', x='age', y='income',
    c='category', colormap='viridis',
    title='Income vs Age', alpha=0.5, figsize=(10, 6))

# Hexbin (for dense scatter)
df.plot(kind='hexbin', x='x', y='y', C='value',
    reduce_C_function=np.mean, gridsize=20,
    title='Hexbin Density')

# Density (KDE)
df['value'].plot(kind='kde', title='Density Estimate')
```

## Subplots

```python
df[['age', 'income', 'experience']].plot(
    subplots=True,
    layout=(2, 2),
    figsize=(12, 8),
    title='Multiple Distributions',
)
```

## Styling

```python
plt.style.use('ggplot')
plt.style.use('seaborn-v0_8-darkgrid')
plt.rcParams['figure.dpi'] = 150
plt.rcParams.update({'font.size': 12, 'axes.titlesize': 14})
```

## Saving

```python
# Save with tight layout
plt.tight_layout()
plt.savefig('plot.png', dpi=300, bbox_inches='tight')
plt.savefig('plot.pdf', bbox_inches='tight')
plt.show()
plt.close()  # Free memory
```

## Advanced Matplotlib Integration

```python
fig, axes = plt.subplots(2, 2, figsize=(14, 10))

# Top-left: time series
axes[0, 0].plot(df['date'], df['value'])
axes[0, 0].set_title('Time Series')

# Top-right: distribution
axes[0, 1].hist(df['age'], bins=30, edgecolor='black')
axes[0, 1].set_title('Age Distribution')

# Bottom-left: bar
axes[1, 0].bar(grouped['category'], grouped['amount'])
axes[1, 0].set_title('Revenue by Category')
axes[1, 0].tick_params(axis='x', rotation=45)

# Bottom-right: scatter with regression
axes[1, 1].scatter(df['age'], df['income'], alpha=0.5)
m, b = np.polyfit(df['age'], df['income'], 1)
axes[1, 1].plot(df['age'], m * df['age'] + b, 'r--')
axes[1, 1].set_title('Income vs Age with Trend')

plt.tight_layout()
plt.show()
```
