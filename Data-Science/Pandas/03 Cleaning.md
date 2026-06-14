---
id: ds-pd-0003-0000-0000-000000000003
title: Pandas Cleaning
language: markdown
tags: [pandas, cleaning, missing-data, duplicates, outliers]
selection: null
isPinned: false
timestamp: 1781700000503
---
	# Pandas Data Cleaning

**Links**: [[01 Basics]] | [[04 Selection Indexing]] | [[05 GroupBy Aggregation]] | [[07 Transform String]] | [[_MOC]]

## Handle Missing Values

```python
# Count nulls per column
df.isna().sum()
df.isnull().sum()   # alias

# Drop
df.dropna()                          # Drop rows with any null
df.dropna(subset=['email'])          # Drop rows where email is null
df.dropna(how='all')                 # Drop rows where ALL values are null
df.dropna(thresh=5)                  # Keep rows with at least 5 non-null values

# Fill
df.fillna(0)                         # Fill with constant
df.fillna(df.mean())                 # Fill with column mean
df.fillna(method='ffill')            # Forward fill (carry last value forward)
df.fillna(method='bfill')            # Backward fill
df['age'].fillna(df['age'].median()) # Fill specific column

# Interpolate
df.interpolate(method='linear')      # Linear interpolation
df.interpolate(method='time')        # Time-based interpolation
df.interpolate(method='quadratic')   # Polynomial interpolation

# Detect missing patterns
missing = df.isna()
missing.sum()                        # Count per column
missing.sum() / len(df)              # Percentage per column
```

## Smart Filling Strategy

```python
def smart_fill_missing(df):
    for col in df.columns:
        if df[col].isna().sum() == 0:
            continue
        if df[col].dtype in ['float64', 'int64']:
            if df[col].skew() > 1:
                df[col] = df[col].fillna(df[col].median())
            else:
                df[col] = df[col].fillna(df[col].mean())
        elif df[col].dtype.name == 'category':
            df[col] = df[col].fillna(
                df[col].mode()[0] if len(df[col].mode()) > 0 else 'Unknown'
            )
        else:
            df[col] = df[col].fillna('Missing')
    return df
```

## Duplicates

```python
df.duplicated().sum()                        # Count of duplicate rows
df.duplicated(subset=['email']).sum()        # Duplicates based on specific columns
df.duplicated(keep=False)                    # Mark all duplicates (not just first)

df.drop_duplicates()
df.drop_duplicates(subset=['email'])         # Keep first by default
df.drop_duplicates(subset=['email'], keep='last')
df.drop_duplicates(subset=['email'], keep=False)  # Drop all rows with duplicate email
```

## Outlier Detection

```python
# Z-score method (3 standard deviations)
z_scores = np.abs(
    (df['salary'] - df['salary'].mean()) / df['salary'].std()
)
df_clean = df[z_scores < 3]

# IQR method
Q1, Q3 = df['salary'].quantile([0.25, 0.75])
IQR = Q3 - Q1
lower, upper = Q1 - 1.5 * IQR, Q3 + 1.5 * IQR
df_clean = df[(df['salary'] >= lower) & (df['salary'] <= upper)]

# Percentile clipping
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

## Type Conversion

```python
# Basic
df['age'] = df['age'].astype(int)
df['date'] = pd.to_datetime(df['date'])
df['category'] = df['category'].astype('category')

# Parse strings to datetime with format
df['date'] = pd.to_datetime(df['date'], format='%Y-%m-%d %H:%M:%S')

# Coerce errors to NaT/NaN
pd.to_numeric(df['col'], errors='coerce')
pd.to_datetime(df['col'], errors='coerce')

# Downcast numeric types
def downcast(df):
    for col in df.select_dtypes(include=['int']).columns:
        df[col] = pd.to_numeric(df[col], downcast='integer')
    for col in df.select_dtypes(include=['float']).columns:
        df[col] = pd.to_numeric(df[col], downcast='float')
    return df
```

## Common Cleaning Pipeline

```python
def clean_dataset(df):
    df = df.copy()
    df = df.drop_duplicates()
    df = df.fillna(method='ffill')
    df = downcast(df)
    if 'date' in df.columns:
        df['date'] = pd.to_datetime(df['date'], errors='coerce')
    return df
```
