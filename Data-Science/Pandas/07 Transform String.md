---
id: ds-pd-0007-0000-0000-000000000007
title: Pandas Transform and String Operations
language: markdown
tags: [pandas, apply, map, string, categorical]
selection: null
isPinned: false
timestamp: 1781700000507
---
# Pandas Transform and String Operations

**Links**: [[01 Basics]] | [[03 Cleaning]] | [[05 GroupBy Aggregation]] | [[_MOC]]

## Apply and Map

```python
# Apply function to each element
df['age_group'] = df['age'].apply(
    lambda x: 'young' if x < 30 else ('mid' if x < 50 else 'senior')
)

# Map values (dictionary-based)
df['city_code'] = df['city'].map({'Portland': 'PDX', 'Seattle': 'SEA'})

# Replace
df['status'] = df['status'].replace({'yes': True, 'no': False})

# Rename columns
df.rename(columns={'name': 'full_name', 'age': 'years'}, inplace=True)

# Add computed columns
df['total_comp'] = df['salary'] + df['bonus']
df['age_squared'] = df['age'] ** 2
```

## String Operations (.str accessor)

```python
# Case conversion
df['name'].str.upper()
df['name'].str.lower()
df['name'].str.title()
df['name'].str.capitalize()

# Contains and matching
df['email'].str.contains('@', na=False)
df['name'].str.startswith('A')
df['name'].str.endswith('son')
df['code'].str.match(r'\d{3}-\d{2}-\d{4}')

# Splitting
df['full_name'].str.split(' ', expand=True)
df['full_name'].str.split(' ', n=1, expand=True)
df['path'].str.rsplit('/', n=1, expand=True)

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

# Length, strip
df['name'].str.len()
df['name'].str.strip()
df['name'].str.lstrip()
df['name'].str.rstrip()
df['name'].str.strip('.,!?')

# Type checking
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
# Create ordered categorical
df['category'] = pd.Categorical(
    df['category'],
    categories=['low', 'medium', 'high', 'vip'],
    ordered=True,
)

# Convert to categorical
df['region'] = df['region'].astype('category')

# Inspect categories
df['category'].cat.codes                   # Integer codes
df['category'].cat.categories              # List of categories
df['category'].cat.ordered                 # Is ordered?

# Modify categories
df['category'].cat.set_categories(['low', 'mid', 'high', 'premium'])
df['category'].cat.add_categories(['ultra'])
df['category'].cat.remove_categories(['low'])

# Rename
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
df.sort_values('category')              # Sorts by categorical order
df.groupby('category')['amount'].mean()

# Memory savings
df['city'].astype('category').memory_usage(deep=True)
# vs
df['city'].memory_usage(deep=True)

# Remove unused
df['cat'].cat.remove_unused_categories()
```
