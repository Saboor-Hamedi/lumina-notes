---
id: a1b2c3d4-1200-4000-8000-000000000200
title: Pandas for Data Analysis
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001200
---
# Pandas for Data Analysis

Pandas is the fundamental Python library for data manipulation and analysis, built on top of NumPy.

## Core Data Structures

| Structure | Description | Axis |
|-----------|-------------|------|
| Series | 1D labeled array | Index |
| DataFrame | 2D labeled table | Index + Columns |
| Index | Label collection | — |

## Creating DataFrames

```python
import pandas as pd
import numpy as np

# From dictionary
df = pd.DataFrame({
    'name': ['Alice', 'Bob', 'Charlie'],
    'age': [30, 25, 35],
    'city': ['Portland', 'Seattle', 'San Francisco'],
    'salary': [75000, 65000, 95000],
    'hired': pd.to_datetime(['2020-01-15', '2021-03-20', '2019-06-01']),
})

# From list of dicts
df = pd.DataFrame([
    {'name': 'Alice', 'age': 30},
    {'name': 'Bob', 'age': 25},
])

# From CSV
df = pd.read_csv('data.csv', parse_dates=['date'], index_col='id')

# From SQL
df = pd.read_sql('SELECT * FROM users', connection)

# From Parquet
df = pd.read_parquet('data.parquet')
```

## Data Inspection

```python
df.head(10)           # First 10 rows
df.tail(5)            # Last 5 rows
df.sample(3)          # Random 3 rows
df.info()             # Column types, non-null count, memory
df.describe()         # Summary statistics (numeric)
df.describe(include='object')  # Summary for categorical cols
df.shape              # (rows, columns)
df.columns            # Column names
df.dtypes             # Column types
df.index              # Index values
df.memory_usage(deep=True)  # Memory usage per column
```

## Selection

```python
# Column selection
df['name']            # Series
df[['name', 'age']]   # DataFrame
df.name               # Series (if valid attribute name)

# Row selection (iloc = integer, loc = label)
df.iloc[0]            # First row
df.iloc[1:5]          # Rows 1-4
df.iloc[[0, 2, 4]]    # Rows 0, 2, 4
df.iloc[1:5, 0:2]     # Rows 1-4, columns 0-1

df.loc[0]             # Row by index label
df.loc[0:5]           # Rows 0-5 (inclusive!)
df.loc[df['age'] > 30]  # Conditional
df.loc[df['age'] > 30, ['name', 'salary']]

# Boolean indexing
df[df['age'] > 30]
df[(df['age'] > 25) & (df['city'] == 'Portland')]
df[df['name'].str.startswith('A')]
df[df['salary'].between(60000, 80000)]
```

## Data Cleaning

```python
# Handle missing values
df.isna().sum()                # Count nulls per column
df.dropna()                    # Drop rows with any null
df.dropna(subset=['email'])    # Drop rows where email is null
df.fillna(0)                   # Fill nulls with 0
df.fillna(df.mean())           # Fill with column mean
df.fillna(method='ffill')      # Forward fill
df['age'].fillna(df['age'].median())  # Fill specific column

# Duplicates
df.duplicated().sum()
df.drop_duplicates()
df.drop_duplicates(subset=['email'])

# Outlier detection
z_scores = np.abs((df['salary'] - df['salary'].mean()) / df['salary'].std())
df[z_scores < 3]  # Keep only rows within 3 std deviations

# Type conversion
df['age'] = df['age'].astype(int)
df['date'] = pd.to_datetime(df['date'])
df['category'] = df['category'].astype('category')
```

## Transformations

```python
# Apply functions
df['age_group'] = df['age'].apply(
    lambda x: 'young' if x < 30 else ('mid' if x < 50 else 'senior')
)

# Map values
df['city_code'] = df['city'].map({'Portland': 'PDX', 'Seattle': 'SEA'})

# Replace
df['status'] = df['status'].replace({'yes': True, 'no': False})

# Rename columns
df.rename(columns={'name': 'full_name', 'age': 'years'}, inplace=True)

# Add computed columns
df['total_comp'] = df['salary'] + df['bonus']
df['age_squared'] = df['age'] ** 2
```

## Grouping and Aggregation

```python
# Group by one column
df.groupby('city')['salary'].mean()
df.groupby('city').agg({
    'salary': ['mean', 'median', 'std'],
    'age': 'mean',
    'name': 'count',
})

# Group by multiple columns
df.groupby(['city', 'department']).agg(
    avg_salary=('salary', 'mean'),
    count=('name', 'count'),
    total=('salary', 'sum'),
).reset_index()

# Pivot tables
pd.pivot_table(df,
    values='salary',
    index='city',
    columns='department',
    aggfunc='mean',
    fill_value=0
)
```

## Merging and Joining

```python
# SQL-style joins
merged = pd.merge(
    df_orders,
    df_customers,
    on='customer_id',
    how='left',           # left, right, inner, outer
    suffixes=('_order', '_customer')
)

# Concatenation
combined = pd.concat([df1, df2], axis=0)  # Row-wise
combined = pd.concat([df1, df2], axis=1)  # Column-wise

# Join on index
df1.join(df2, how='inner')
```

## Time Series

```python
# Set datetime index
df.set_index('date', inplace=True)

# Resampling
df.resample('D').mean()       # Daily
df.resample('W').sum()        # Weekly
df.resample('ME').sum()       # Month end
df.resample('Q').mean()       # Quarterly

# Rolling windows
df['rolling_avg'] = df['value'].rolling(window=7).mean()
df['expanding'] = df['value'].expanding().mean()

# Shifting
df['prev_day'] = df['value'].shift(1)
df['pct_change'] = df['value'].pct_change()
df['diff'] = df['value'].diff()

# Date features
df['year'] = df.index.year
df['month'] = df.index.month
df['day'] = df.index.day
df['dayofweek'] = df.index.dayofweek
```

## Performance Optimization

```python
# Use vectorized operations (avoid apply)
df['total'] = df['quantity'] * df['price']  # Fast
df['total'] = df.apply(lambda r: r['quantity'] * r['price'], axis=1)  # Slow

# Use categorical for low-cardinality strings
df['city'] = df['city'].astype('category')

# Use inplace when possible
df.drop(columns=['temp'], inplace=True)

# Chunk processing for large datasets
chunks = pd.read_csv('large.csv', chunksize=10000)
results = []
for chunk in chunks:
    results.append(chunk.groupby('city')['value'].sum())
final = pd.concat(results).groupby(level=0).sum()

## DataFrame/Series Internals Diagram

```mermaid
graph TB
    subgraph "Pandas Architecture"
        subgraph "DataFrame"
            DF[DataFrame]
            DF --> C1[Column 1: Series]
            DF --> C2[Column 2: Series]
            DF --> C3[Column 3: Series]
        end
        subgraph "Series"
            S[Series]
            S --> V[Values: ndarray]
            S --> I[Index: Index object]
            S --> D[dtype]
            S --> N[name]
        end
        subgraph "Index"
            IDX[Index]
            IDX --> LI[Labels]
            IDX --> DI[DatetimeIndex]
            IDX --> MI[MultiIndex]
            IDX --> RI[RangeIndex]
            IDX --> PI[PeriodIndex]
        end
        subgraph "Underlying"
            NP[NumPy ndarray]
            BL[BlockManager]
        end
        DF --> C1 & C2 & C3
        C1 & C2 & C3 --> S
        S --> V --> NP
        DF --> BL
    end

    subgraph "Operations Flow"
        RAW[Raw Data] --> CREATE[Constructor]
        CREATE --> DF
        DF --> SELECT[Selection: loc/iloc]
        DF --> FILTER[Filter: bool indexing]
        DF --> GROUP[GroupBy]
        GROUP --> AGG[Aggregation]
        GROUP --> TRANS[Transform]
        GROUP --> FILT_GRP[Filter]
        DF --> MERGE[merge/join/concat]
        DF --> RESHAPE[stack/unstack/pivot/melt]
        DF --> TS[Time Series: resample/rolling]
    end
```

## Advanced Indexing — MultiIndex

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
df_mi.xs('A')                           # All 'A' rows (drops level)
df_mi.xs(('A', 'North'))                # Specific row
df_mi.xs('North', level='direction')    # All North regardless of region

# Cross-section on columns
df_cols.xs('revenue', axis=1, level=1)  # All revenue columns

# get_level_values
df_mi.index.get_level_values('region')
df_mi.index.get_level_values(0)

# swaplevel and reorder_levels
df_mi.swaplevel()
df_mi.reorder_levels(['direction', 'region'])

# sort_index for slicing
df_mi.sort_index()
df_mi.loc[('A', 'North'):('B', 'South')]  # Slice after sort

# Partial selection
df_mi.loc['A']                     # All rows with region A
df_mi.loc[('A', 'North')]          # Specific row
df_mi.loc['A':'B']                 # Range on first level
```

## Window Functions

```python
# Rolling with custom functions
df['rolling_custom'] = (
    df['value']
    .rolling(window=5, min_periods=2)
    .apply(lambda x: x.max() - x.min(), raw=False)
)

# Rolling with multiple metrics
def rolling_stats(series):
    return pd.Series({
        'mean': series.mean(),
        'std': series.std(),
        'skew': series.skew()
    })

df_rolling = (
    df['value']
    .rolling(10)
    .apply(rolling_stats, raw=False)
)

# Expanding window
df['expanding_mean'] = df['value'].expanding().mean()
df['expanding_sum'] = df['value'].expanding().sum()
df['expanding_cust'] = df['value'].expanding().apply(
    lambda x: x.quantile(0.75) - x.quantile(0.25)
)

# Exponentially weighted moving (EWM)
df['ewm_halflife'] = df['value'].ewm(halflife=5).mean()
df['ewm_span'] = df['value'].ewm(span=12, adjust=False).mean()
df['ewm_com'] = df['value'].ewm(com=0.5).std()

# Rolling on grouped data
df['group_rolling_avg'] = (
    df.groupby('category')['value']
    .transform(lambda x: x.rolling(3, min_periods=1).mean())
)
```

## Advanced GroupBy

```python
# Transform — returns same shape as original
df['pct_of_group'] = (
    df.groupby('region')['amount']
    .transform(lambda x: x / x.sum() * 100)
)

df['z_score'] = (
    df.groupby('category')['value']
    .transform(lambda x: (x - x.mean()) / x.std())
)

# Filter groups
df.groupby('customer').filter(lambda g: g['amount'].sum() > 1000)
df.groupby('region').filter(lambda g: len(g) >= 10)

# Apply custom aggregation
def top_two(series):
    return series.nlargest(2).sum()

df.groupby('region')['amount'].apply(top_two)

# Multiple custom aggregations
df.groupby('region').agg(
    total_revenue=('amount', 'sum'),
    avg_revenue=('amount', 'mean'),
    top_two_sum=('amount', top_two),
    n_customers=('customer_id', 'nunique'),
    first_date=('date', 'min'),
    last_date=('date', 'max'),
)

# Named aggregation
df.groupby(['region', 'category']).agg(
    total=('amount', 'sum'),
    mean=('amount', 'mean'),
    std=('amount', 'std'),
    count=('amount', 'count'),
)

# GroupBy with multiple functions per column
df.groupby('region').agg({
    'amount': ['sum', 'mean', 'std', 'count'],
    'date': ['min', 'max'],
    'customer_id': 'nunique',
})
```

## Stack/Unstack/Melt/Pivot

```python
# Setup
df_wide = pd.DataFrame({
    'id': [1, 2, 3],
    'Q1': [100, 200, 150],
    'Q2': [110, 210, 160],
    'Q3': [120, 220, 170],
    'Q4': [130, 230, 180],
}).set_index('id')

# Stack: wide → long (pivots columns into rows)
df_long = df_wide.stack().reset_index(name='revenue')
df_long.columns = ['id', 'quarter', 'revenue']

# Unstack: long → wide
df_wide_back = df_long.set_index(['id', 'quarter']).unstack()

# Melt: explicit unpivot
df_melted = pd.melt(
    df_wide.reset_index(),
    id_vars=['id'],
    value_vars=['Q1', 'Q2', 'Q3', 'Q4'],
    var_name='quarter',
    value_name='revenue',
)

# pivot: long → wide (general)
df_pivoted = df_long.pivot(
    index='id',
    columns='quarter',
    values='revenue',
)

# pivot_table: with aggregation
pd.pivot_table(
    df,
    values='amount',
    index='region',
    columns='category',
    aggfunc='sum',
    fill_value=0,
    margins=True,
    margins_name='Total',
)

# crosstab (frequency tables)
pd.crosstab(
    index=df['region'],
    columns=df['category'],
    values=df['amount'],
    aggfunc='sum',
    normalize='index',  # row percentages
    margins=True,
)
```

## String Operations (.str accessor)

```python
# Case conversion
df['name'].str.upper()
df['name'].str.lower()
df['name'].str.title()
df['name'].str.capitalize()
df['name'].str.swapcase()

# Contains and matching
df['email'].str.contains('@', na=False)
df['name'].str.startswith('A')
df['name'].str.endswith('son')
df['code'].str.match(r'\d{3}-\d{2}-\d{4}')  # Regex match

# Splitting
df['full_name'].str.split(' ', expand=True)  # Returns DataFrame
df['full_name'].str.split(' ', n=1, expand=True)  # Limit splits
df['path'].str.rsplit('/', n=1, expand=True)  # Right split

# Extraction
df['email'].str.extract(r'(\w+)@(\w+)\.(\w+)', expand=True)
df['email'].str.extractall(r'(\w+)@(\w+)\.(\w+)')
df['phone'].str.extract(r'(\d{3})-(\d{3})-(\d{4})')

# Replacing
df['text'].str.replace('old', 'new', regex=False)
df['text'].str.replace(r'\d+', '[REDACTED]', regex=True)
df['text'].str.replace(r'\s+', ' ', regex=True)

# Padding and slicing
df['code'].str.zfill(5)
df['code'].str.pad(10, side='right', fillchar='-')
df['name'].str.slice(0, 3)
df['name'].str[0:3]

# Length, strip, and other utilities
df['name'].str.len()
df['name'].str.strip()
df['name'].str.lstrip()
df['name'].str.rstrip()
df['name'].str.strip('.,!?')

# Check types
df['name'].str.isalpha()
df['code'].str.isdigit()
df['name'].str.isalnum()
df['name'].str.isspace()

# Concatenation
df['first'].str.cat(df['last'], sep=' ')
df[['first', 'last']].agg(' '.join, axis=1)

# Encoding
df['name'].str.encode('utf-8').str.decode('utf-8')

# Repeat
df['name'].str.repeat(2)
```

## Categorical Data

```python
# Create categorical
df['category'] = pd.Categorical(
    df['category'],
    categories=['low', 'medium', 'high', 'vip'],
    ordered=True,
)

# Convert to categorical
df['region'] = df['region'].astype('category')

# Operations
df['category'].cat.codes                    # Integer codes
df['category'].cat.categories               # List of categories
df['category'].cat.ordered                  # Is ordered?
df['category'].cat.set_categories(['low', 'mid', 'high', 'premium'])
df['category'].cat.add_categories(['ultra'])
df['category'].cat.remove_categories(['low'])

# Rename categories
df['category'].cat.rename_categories({
    'low': 'bronze',
    'medium': 'silver',
    'high': 'gold',
    'vip': 'platinum',
})

# Reorder
df['category'] = df['category'].cat.reorder_categories(
    ['platinum', 'gold', 'silver', 'bronze']
)

# Sorting with ordered categoricals
df.sort_values('category')  # Sorts by categorical order
df.groupby('category')['amount'].mean()

# Memory savings
df['city'].astype('category').memory_usage(deep=True)
# vs
df['city'].memory_usage(deep=True)

# Unused category handling
df['cat'].cat.remove_unused_categories()
```

## Visualization with .plot

```python
import matplotlib.pyplot as plt

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
df['age'].plot(
    kind='hist',
    bins=30,
    alpha=0.7,
    title='Age Distribution',
    edgecolor='black',
)

# Box plot
df[['age', 'income', 'experience']].plot(
    kind='box',
    title='Distribution Summary',
)

# Area plot
df.groupby('date')['amount'].sum().plot(kind='area', alpha=0.5)

# Scatter plot
df.plot(
    kind='scatter',
    x='age',
    y='income',
    c='category',
    colormap='viridis',
    title='Income vs Age',
    alpha=0.5,
    figsize=(10, 6),
)

# Hexbin (dense scatter)
df.plot(
    kind='hexbin',
    x='x',
    y='y',
    C='value',
    reduce_C_function=np.mean,
    gridsize=20,
    title='Hexbin Density',
)

# Density (KDE)
df['value'].plot(kind='kde', title='Density Estimate')

# Subplots
df[['age', 'income', 'experience']].plot(
    subplots=True,
    layout=(2, 2),
    figsize=(12, 8),
    title='Multiple Distributions',
)

# Custom styling
plt.style.use('ggplot')
plt.style.use('seaborn-v0_8-darkgrid')
plt.rcParams['figure.dpi'] = 150
plt.rcParams.update({'font.size': 12, 'axes.titlesize': 14})
```

## Working with DateTime

```python
# Creating datetime ranges
dates = pd.date_range(
    start='2024-01-01',
    end='2024-12-31',
    freq='D',  # Daily
)
dates_hourly = pd.date_range('2024-01-01', periods=24, freq='h')
dates_business = pd.date_range('2024-01-01', periods=20, freq='B')

# Period and Timedelta
period = pd.Period('2024-Q1', freq='Q')
periods = pd.period_range('2024-01', periods=12, freq='M')

# Timedelta
df['duration'] = df['end_date'] - df['start_date']
df['duration_days'] = df['duration'].dt.days
df['duration_hours'] = df['duration'].dt.total_seconds() / 3600

# Date arithmetic
df['date'] + pd.Timedelta(days=7)
df['date'] + pd.offsets.MonthEnd()
df['date'] + pd.offsets.BusinessDay(5)

# Date components
df['date'].dt.year
df['date'].dt.month
df['date'].dt.day
df['date'].dt.dayofweek       # Mon=0, Sun=6
df['date'].dt.dayofyear
df['date'].dt.quarter
df['date'].dt.is_month_start
df['date'].dt.is_month_end
df['date'].dt.is_quarter_end
df['date'].dt.is_year_end
df['date'].dt.days_in_month

# Floor/ceil date
df['date'].dt.floor('D')
df['date'].dt.ceil('h')

# Timezone handling
df['date_utc'] = df['date'].dt.tz_localize('UTC')
df['date_est'] = df['date_utc'].dt.tz_convert('US/Eastern')

# Custom business days
from pandas.tseries.offsets import CustomBusinessDay
us_holidays = pd.tseries.holiday.USFederalHolidayCalendar()
bday_us = CustomBusinessDay(calendar=us_holidays)
df['next_business'] = df['date'] + bday_us
```

## I/O Deep Dive

```python
# CSV - full control
df.to_csv('output.csv',
    sep=',',
    index=False,
    encoding='utf-8-sig',
    compression='gzip',
    date_format='%Y-%m-%d',
    columns=['id', 'name', 'amount'],
    header=True,
    quoting=1,  # QUOTE_ALL
)

df = pd.read_csv('data.csv',
    sep=',',
    header=0,
    names=['id', 'name', 'age', 'salary'],  # Override names
    dtype={'id': 'Int32', 'age': 'Int32'},  # Nullable integers
    parse_dates=['date'],
    date_parser=lambda x: pd.to_datetime(x, format='%Y-%m-%d'),
    na_values=['', 'NA', 'NULL', '-'],
    keep_default_na=True,
    skiprows=0,
    nrows=10000,
    usecols=lambda c: c not in ['temp_col'],
    chunksize=5000,
    encoding='utf-8',
    memory_map=True,
)

# Excel - reading
df = pd.read_excel('data.xlsx',
    sheet_name='Sheet1',
    header=0,
    usecols='A:F',
    dtype={'id': str, 'amount': float},
    parse_dates=['date'],
    na_values='NA',
)

# Excel - writing with multiple sheets
with pd.ExcelWriter('report.xlsx', engine='openpyxl') as writer:
    df_summary.to_excel(writer, sheet_name='Summary', index=False)
    df_detail.to_excel(writer, sheet_name='Detail', index=False)
    df_pivot.to_excel(writer, sheet_name='Pivot', index=True)

    # Auto-adjust column widths
    for sheet_name in writer.sheets:
        worksheet = writer.sheets[sheet_name]
        for col in worksheet.columns:
            max_length = max(len(str(cell.value or '')) for cell in col)
            worksheet.column_dimensions[col[0].column_letter].width = min(max_length + 2, 50)

# SQL
from sqlalchemy import create_engine

engine = create_engine('postgresql://user:pass@localhost:5432/db')
df = pd.read_sql_query('SELECT * FROM orders WHERE date >= %(cutoff)s',
    engine, params={'cutoff': '2024-01-01'})
df.to_sql('orders_staging', engine, if_exists='replace', index=False, chunksize=1000)

# Parquet
df.to_parquet('data.parquet',
    engine='pyarrow',
    compression='snappy',
    index=False,
    partition_cols=['region'],
)
df = pd.read_parquet('data.parquet', engine='pyarrow')

# Feather (fast binary)
df.to_feather('data.feather')
df = pd.read_feather('data.feather')

# Pickle
df.to_pickle('data.pkl.gz', compression='gzip')
df = pd.read_pickle('data.pkl.gz')

# Clipboard (for quick copy-paste)
df.to_clipboard(index=False)
df_from_clip = pd.read_clipboard()

# JSON
df.to_json('data.json', orient='records', date_format='iso', indent=2)
df = pd.read_json('data.json', orient='records')

# HTML
df.to_html('table.html', index=False, border=0, classes='table table-striped')

# Stata/SPSS/SAS
df.to_stata('export.dta', version=118)
df = pd.read_stata('data.dta')
```

## Memory Optimization

```python
# Check memory usage
df.info(memory_usage='deep')
df.memory_usage(deep=True)

# Downcast numeric types
def downcast(df):
    for col in df.select_dtypes(include=['int']).columns:
        df[col] = pd.to_numeric(df[col], downcast='integer')
    for col in df.select_dtypes(include=['float']).columns:
        df[col] = pd.to_numeric(df[col], downcast='float')
    return df

df = downcast(df)

# Use nullable integer types
df['nullable_int'] = pd.array([1, 2, None], dtype='Int32')  # Capital I

# Categorical for low-cardinality strings
for col in df.select_dtypes(include='object').columns:
    if df[col].nunique() / len(df) < 0.5:  # < 50% unique
        df[col] = df[col].astype('category')

# Sparse data
df['sparse_col'] = pd.arrays.SparseArray(df['sparse_col'], fill_value=0)

# Use smaller types
int_types = {
    'int8': (-128, 127),
    'int16': (-32768, 32767),
    'int32': (-2147483648, 2147483647),
    'int64': (-9223372036854775808, 9223372036854775807),
}

def safe_downcast_int(series):
    mn, mx = series.min(), series.max()
    for dtype, (lo, hi) in int_types.items():
        if mn >= lo and mx <= hi:
            return series.astype(dtype)
    return series

# Chunked processing for memory management
chunks = []
for chunk in pd.read_csv('huge.csv', chunksize=50000, low_memory=False):
    chunk = downcast(chunk)
    # process...
    chunks.append(chunk)
result = pd.concat(chunks, ignore_index=True)
```

## Advanced Merge

```python
# Merge with indicator
merged = pd.merge(
    df_orders,
    df_customers,
    on='customer_id',
    how='outer',
    indicator=True,  # Adds '_merge' column
)

merged['_merge'].value_counts()  # left_only, right_only, both

# Validate merge relationships
pd.merge(df_orders, df_customers, on='customer_id',
    validate='m:1')               # Many-to-one

pd.merge(df_parent, df_child, on='parent_id',
    validate='1:m')               # One-to-many

pd.merge(df_a, df_b, on='id',
    validate='1:1')               # One-to-one

# Cross join (cartesian product)
df_cross = pd.merge(
    df_products.assign(key=1),
    df_dates.assign(key=1),
    on='key',
).drop('key', axis=1)

# Multiple column merge
pd.merge(df_a, df_b,
    left_on=['first', 'last'],
    right_on=['first_name', 'last_name'],
    how='left',
    suffixes=('_left', '_right'),
)

# Merge on index
pd.merge(df_a, df_b,
    left_index=True,
    right_on='customer_id',
)

# Combine_first (fill missing from another df)
df_a.combine_first(df_b)  # df_a values where non-null, else df_b

# Update in place
df_a.update(df_b)  # Modifies df_a where df_b has non-null values

# concat with keys
pd.concat([df_q1, df_q2, df_q3, df_q4],
    keys=['Q1', 'Q2', 'Q3', 'Q4'],
    axis=0,
)

# concat with ignore_index
pd.concat([df1, df2], ignore_index=True)
```

## Practical Data Science Recipes

```python
# Train/Test split (stratified)
from sklearn.model_selection import train_test_split

train, test = train_test_split(
    df, test_size=0.2, random_state=42, stratify=df['target']
)

# Feature engineering recipe
def engineer_features(df):
    df = df.copy()

    # Date features
    if 'date' in df.columns:
        df['year'] = df['date'].dt.year
        df['month'] = df['date'].dt.month
        df['day_of_week'] = df['date'].dt.dayofweek
        df['is_weekend'] = df['date'].dt.dayofweek >= 5
        df['quarter'] = df['date'].dt.quarter
        df['elapsed_days'] = (df['date'] - df['date'].min()).dt.days

    # Text features
    if 'text' in df.columns:
        df['text_length'] = df['text'].str.len()
        df['word_count'] = df['text'].str.split().str.len()
        df['has_caps'] = df['text'].str.contains(r'[A-Z]', na=False)

    # Interaction features
    if all(c in df.columns for c in ['age', 'income']):
        df['age_income_ratio'] = df['income'] / (df['age'] + 1)
        df['income_rank'] = df['income'].rank(pct=True)

    # Aggregations (if group col present)
    if 'customer_id' in df.columns:
        customer_stats = df.groupby('customer_id').agg(
            avg_amount=('amount', 'mean'),
            total_amount=('amount', 'sum'),
            transaction_count=('amount', 'count'),
            recency=('date', 'max'),
        )
        df = df.merge(customer_stats, on='customer_id', how='left')
        df['days_since_last'] = (df['date'] - df['recency']).dt.days

    return df

# Missing value strategy
def smart_fill_missing(df):
    for col in df.columns:
        if df[col].isna().sum() == 0:
            continue

        if df[col].dtype in ['float64', 'int64']:
            # Use median for skewed, mean for normal
            if df[col].skew() > 1:
                df[col] = df[col].fillna(df[col].median())
            else:
                df[col] = df[col].fillna(df[col].mean())
        elif df[col].dtype.name == 'category':
            df[col] = df[col].fillna(df[col].mode()[0] if len(df[col].mode()) > 0 else 'Unknown')
        else:
            df[col] = df[col].fillna('Missing')
    return df

# Outlier capping
def cap_outliers(df, columns=None, method='iqr'):
    if columns is None:
        columns = df.select_dtypes(include='number').columns
    df = df.copy()
    for col in columns:
        if method == 'iqr':
            Q1, Q3 = df[col].quantile([0.25, 0.75])
            IQR = Q3 - Q1
            lower, upper = Q1 - 1.5 * IQR, Q3 + 1.5 * IQR
        elif method == 'percentile':
            lower, upper = df[col].quantile([0.01, 0.99])
        df[col] = df[col].clip(lower, upper)
    return df
```

## Pandas vs Polars

```mermaid
graph TD
    subgraph "Pandas"
        P1[Eager Execution]
        P2[Row-based API]
        P3[NumPy Backend]
        P4[Index-centric]
        P5[Well-documented]
        P6[Mature Ecosystem]
    end
    subgraph "Polars"
        L1[Eager + Lazy Execution]
        L2[Expression-based API]
        L3[Arrow Backend]
        L4[No Index - Columnar]
        L5[Growing Docs]
        L6[Emerging Ecosystem]
    end

    P1 -.->|Similar| L1
    P2 -.->|Different| L2
    P3 -.->|Different| L3
    P4 -.->|Different| L4
```

| Feature | Pandas | Polars |
|---------|--------|--------|
| Execution | Eager only | Eager + Lazy (query optimization) |
| Backend | NumPy | Apache Arrow |
| Memory | ~2x data size | ~1x data size (zero-copy) |
| Multi-core | No (single core) | Yes (automatic parallel) |
| Index | Central concept | No index (row number) |
| API style | Method chaining | Expression-based |
| GroupBy | `df.groupby('col').agg({'val': 'sum'})` | `df.group_by('col').agg(pl.col('val').sum())` |
| Missing data | NaN, None, NaT | null (unified) |
| String ops | `.str` accessor | `.str` namespace |
| Window functions | `.transform()` | `.over()` |
| Speed | Baseline | 5-30x faster on large data |
| Ecosystem integration | sklearn, matplotlib, seaborn, etc. | Growing (hvPlot, etc.) |

```python
# Polars example (for comparison)
import polars as pl

# Read CSV
df_pl = pl.read_csv('data.csv')

# Lazy execution
q = (
    df_pl.lazy()
    .filter(pl.col('amount') > 0)
    .group_by('region')
    .agg([
        pl.col('amount').sum().alias('total'),
        pl.col('amount').mean().alias('avg'),
        pl.col('amount').count().alias('count'),
    ])
    .sort('total', descending=True)
)
result = q.collect()  # Execute optimized plan
```

## Complete Data Pipeline

```mermaid
flowchart LR
    subgraph "Ingest"
        CSV[CSV Files] --> READ[read_csv]
        DB[Database] --> SQL[read_sql]
        API[API] --> JSON[read_json]
        Excel[Excel] --> XL[read_excel]
    end
    subgraph "Clean"
        READ & SQL & JSON & XL --> MISS[Handle Missing]
        MISS --> DEDUP[Remove Duplicates]
        DEDUP --> TYPES[Fix Types]
        TYPES --> OUTLIERS[Outlier Treatment]
    end
    subgraph "Transform"
        OUTLIERS --> FILTER[Filter Columns]
        FILTER --> ENGINEER[Feature Engineering]
        ENGINEER --> AGG[Aggregate]
        AGG --> JOIN[Join Datasets]
    end
    subgraph "Output"
        JOIN --> EXPORT[to_csv]
        JOIN --> DBLOAD[to_sql]
        JOIN --> PARQ[to_parquet]
        JOIN --> VIZ[Plotting]
    end

    style Ingest fill:#e3f2fd
    style Clean fill:#bbdefb
    style Transform fill:#90caf9
    style Output fill:#64b5f6
```

## Quick Reference Table

| Operation | Pandas Code | Notes |
|-----------|-------------|-------|
| Read CSV | `pd.read_csv('file.csv')` | Use `parse_dates`, `dtype`, `chunksize` |
| Filter | `df[df['col'] > 0]` | Or `.query('col > 0')` |
| Group sum | `df.groupby('g')['v'].sum()` | `.agg()` for multiple |
| Merge | `pd.merge(a, b, on='k', how='left')` | `validate`, `indicator`, `suffixes` |
| Pivot | `df.pivot(index, columns, values)` | `pivot_table` with aggfunc |
| Resample | `df.resample('M').mean()` | Requires datetime index |
| Rolling | `df['v'].rolling(7).mean()` | `.apply(custom_fn)` |
| Shift | `df['v'].shift(1)` | `-1` for forward |
| One-hot | `pd.get_dummies(df['col'])` | `drop_first=True` |
| Rank | `df['v'].rank()` | `method='dense'`, `pct=True` |
| Quantile | `df['v'].quantile([0.25, 0.75])` | Returns Series |
| Sample | `df.sample(frac=0.1)` | `random_state`, `weights` |
| Rename | `df.rename(columns={'old': 'new'})` | Or `df.columns = [...]` |
| Drop | `df.drop(columns=['x'])` | `inplace=True` for in-place |
| Sort | `df.sort_values('col', ascending=False)` | `by` for multiple cols |
| Unique | `df['col'].unique()` | `nunique()` for count |
| Value counts | `df['col'].value_counts(normalize=True)` | `dropna=False` |
| Correlation | `df.corr()` | `method='spearman'` |
| Cross-tab | `pd.crosstab(df.a, df.b, margins=True)` | Normalize options |
```
