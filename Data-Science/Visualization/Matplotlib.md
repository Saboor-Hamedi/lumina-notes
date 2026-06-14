---
id: ds-viz-0001-0000-0000-000000000001
title: Matplotlib Deep Dive
language: markdown
tags: [matplotlib, visualization, plotting]
selection: null
isPinned: false
timestamp: 1781700000701
---
# Matplotlib Deep Dive

**Links**: [[_MOC]] | [[Seaborn]] | [[Plotly]]

## Figure and Axes API

Matplotlib has two APIs. Always use the **explicit Axes API** (not pyplot state machine).

```python
import matplotlib.pyplot as plt
import numpy as np

# GOOD: explicit Axes API
fig, ax = plt.subplots(figsize=(10, 6))
ax.plot(x, y, color='blue', linewidth=2)
ax.set_title('Title')
ax.set_xlabel('X axis')
fig.tight_layout()
plt.show()

# BAD: pyplot state machine (avoid for complex plots)
plt.plot(x, y)
plt.title('Title')
plt.show()
```

## Subplots

```python
# Grid of subplots
fig, axes = plt.subplots(2, 3, figsize=(15, 8))

axes[0, 0].plot(x, y)
axes[0, 1].scatter(x, y)
axes[1, 0].hist(data, bins=30)

# Shared axes
fig, axes = plt.subplots(3, 1, sharex=True, figsize=(10, 8))

# Uneven grids
import matplotlib.gridspec as gridspec
fig = plt.figure(figsize=(12, 8))
gs = gridspec.GridSpec(3, 3, figure=fig)
ax1 = fig.add_subplot(gs[0, :])    # Top row, all 3 cols
ax2 = fig.add_subplot(gs[1, :-1])  # Middle row, first 2 cols
ax3 = fig.add_subplot(gs[1:, -1])  # Right col, last 2 rows
```

## Plot Types

```python
fig, ax = plt.subplots()

ax.plot(x, y, 'b-', label='Line')          # Line plot
ax.scatter(x, y, c='red', s=50)             # Scatter plot
ax.bar(categories, values)                  # Bar chart
ax.barh(categories, values)                 # Horizontal bar
ax.hist(data, bins=30, density=True)        # Histogram
ax.boxplot([data1, data2], labels=['A','B']) # Box plot
ax.violinplot([data1, data2])               # Violin plot
ax.fill_between(x, y1, y2, alpha=0.3)       # Area fill
ax.stackplot(x, y1, y2, y3)                 # Stacked area
ax.stem(x, y)                               # Stem plot
ax.errorbar(x, y, yerr=error)               # Error bars
ax.imshow(image, cmap='viridis')             # Image display
ax.contour(X, Y, Z)                         # Contour plot
ax.pcolormesh(X, Y, Z, shading='auto')      # Color mesh
```

## Customization

```python
fig, ax = plt.subplots(figsize=(10, 6))

# Colors
ax.plot(x, y, color='#2E86AB', alpha=0.8)
ax.scatter(x, y, c=y, cmap='viridis', s=50)

# Lines and markers
ax.plot(x, y, linestyle='--', linewidth=2,
    marker='o', markersize=6, markerfacecolor='white')

# Labels and title
ax.set_title('Main Title', fontsize=16, fontweight='bold')
ax.set_xlabel('X Axis (units)', fontsize=12)
ax.set_ylabel('Y Axis (units)', fontsize=12)

# Limits and ticks
ax.set_xlim(0, 100)
ax.set_ylim(-1, 1)
ax.set_xticks(np.arange(0, 101, 20))
ax.tick_params(axis='x', rotation=45)

# Grid and legend
ax.grid(True, alpha=0.3, linestyle=':')
ax.legend(loc='best', framealpha=0.9)

# Secondary axis
ax2 = ax.twinx()
ax2.plot(x, y2, color='red')
ax2.set_ylabel('Secondary Axis', color='red')
```

## Saving

```python
fig.savefig('plot.png', dpi=300, bbox_inches='tight')
fig.savefig('plot.pdf', bbox_inches='tight')
fig.savefig('plot.svg', format='svg')
plt.close(fig)  # Free memory
```

## Styles

```python
print(plt.style.available)
# ['ggplot', 'seaborn-v0_8', 'fivethirtyeight',
#  'dark_background', 'bmh', 'classic', 'grayscale']

plt.style.use('ggplot')
plt.style.use('seaborn-v0_8-darkgrid')

# Context manager (temporary style)
with plt.style.context('dark_background'):
    fig, ax = plt.subplots()
    ax.plot(x, y)
```

## Color Maps

```python
cmap = plt.cm.viridis
colors = cmap(np.linspace(0, 1, 10))

# Diverging: coolwarm, RdBu, PiYG
# Sequential: viridis, plasma, inferno, magma
# Qualitative: Set1, Set2, Pastel1, tab10
# Cyclic: twlight_shifted, hsv
```
