---
id: ds-pd-0004-0000-0000-000000000004
title: Pandas Selection and Indexing
language: markdown
tags: [pandas, indexing, loc, iloc, boolean, multiindex]
selection: null
isPinned: false
timestamp: 1781700000504
---
# Pandas Selection and Indexing

**Links**: [[01 Basics]] | [[03 Cleaning]] | [[05 GroupBy Aggregation]] | [[10 Advanced]] | [[_MOC]]

## Column Selection

```python
df['name']                              # Series
df[['name', 'age']]                     # DataFrame
df.name                                 # Series (if valid attribute name)
```

## Row Selection

```python
# iloc = integer-location based
df.iloc[0]                              # First row
df.iloc[1:5]                            # Rows 1-4
df.iloc[[0, 2, 4]]                      # Rows 0, 2, 4
df.iloc[1:5, 0:2]                       # Rows 1-4, columns 0-1

# loc = label-based
df.loc[0]                               # Row by index label
df.loc[0:5]                             # Rows 0-5 (inclusive!)
df.loc[df['age'] > 30]                  # Conditional
df.loc[df['age'] > 30, ['name', 'salary']]  # Conditional + column select
```

## Boolean Indexing

```python
df[df['age'] > 30]
df[(df['age'] > 25) & (df['city'] == 'Portland')]
df[(df['age'] > 25) | (df['city'] == 'Seattle')]
df[~(df['age'] < 18)]

# String conditions
df[df['name'].str.startswith('A')]
df[df['email'].str.contains('@', na=False)]
df[df['code'].str.match(r'\d{3}-\d{2}-\d{4}')]

# Numeric conditions
df[df['salary'].between(60000, 80000)]
df[df['age'].isin([25, 30, 35])]
df[df['age'].notna()]
```

## MultiIndex — Deep Dive

```python
# Create MultiIndex DataFrame
arrays = [
    ['A', 'A', 'A', 'B', 'B', 'B'],
    ['North', 'South', 'East', 'North', 'South', 'East']
]
index = pd.MultiIndex.from_arrays(arrays, names=['region', 'direction'])
df_mi = pd.DataFrame({
    'sales': [100, 150, 120, 200, 180, 160],
    'costs': [70, 95, 80, 130, 115, 105]
}, index=index)

# Column MultiIndex
columns = pd.MultiIndex.from_tuples([
    ('Q1', 'revenue'), ('Q1', 'cost'),
    ('Q2', 'revenue'), ('Q2', 'cost')
])
df_cols = pd.DataFrame(np.random.randn(4, 4), columns=columns)

# Selection with xs
df_mi.xs('A')                                  # All 'A' rows (drops level)
df_mi.xs(('A', 'North'))                       # Specific row
df_mi.xs('North', level='direction')           # All North regardless of region
df_cols.xs('revenue', axis=1, level=1)         # All revenue columns

# get_level_values
df_mi.index.get_level_values('region')
df_mi.index.get_level_values(0)

# swaplevel and reorder_levels
df_mi.swaplevel()
df_mi.reorder_levels(['direction', 'region'])

# sort_index for slicing
df_mi.sort_index()
df_mi.loc[('A', 'North'):('B', 'South')]

# Partial selection
df_mi.loc['A']                                 # All rows with region A
df_mi.loc[('A', 'North')]                      # Specific row
df_mi.loc['A':'B']                             # Range on first level
```

## .query() — SQL-like Filtering

```python
df.query('age > 30')
df.query('age > 30 and city == "Portland"')
df.query('city in ["Portland", "Seattle"]')
df.query('age > @threshold')                   # Use variables with @
df.query('name.str.startswith("A")', engine='python')
```

## Setting Values

```python
df.loc[0, 'name'] = 'Alice Smith'
df.loc[df['age'] > 30, 'senior'] = True
df.iloc[0, 0] = 'New Name'
df['new_col'] = 0                              # Add column with default value
df['new_col'] = df['col1'] + df['col2']        # Derived column
```
