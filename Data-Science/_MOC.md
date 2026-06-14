---
id: ds-moc-0000-0000-0000-000000000001
title: Data Science — Map of Content
language: markdown
tags: [moc, index, datascience, pandas, numpy, visualization]
selection: null
isPinned: true
timestamp: 1781700000099
---
# Data Science — Map of Content

Data science extracts insights from data through statistical analysis, visualization, and machine learning. This folder now contains dedicated sub-folders for the core Python data science stack: **Pandas** for data wrangling, **NumPy** for numerical computing, and **Visualization** (Matplotlib, Seaborn, Plotly) for exploratory and presentation graphics. Each sub-folder is organized as a progressive learning path.

**Parent**: [[_MOC|Master Index]]

## Sub-Areas

| Area | Files | Covers |
|------|-------|--------|
| [[Pandas/_MOC\|Pandas]] | 11 | Basics, I-O, cleaning, indexing, groupby, merge, transform, time series, viz, advanced, end-to-end pipeline |
| [[NumPy/_MOC\|NumPy]] | 7 | Arrays, indexing, broadcasting, ufuncs, linear algebra, random, performance |
| [[Visualization/_MOC\|Visualization]] | 4 | Matplotlib, Seaborn, Plotly — comparison and decision guide |

```mermaid
graph LR
    subgraph DS["Data Science Skills"]
        NUMPY["NumPy<br/>Array computing"] --> PANDAS["Pandas<br/>Data wrangling"]
        PANDAS --> VIZ["Visualization<br/>Matplotlib / Seaborn / Plotly"]
        PANDAS --> ML["Machine Learning<br/>scikit-learn (see AI/ML)"]
        NUMPY --> VIZ
    end
```

## Learning Path

```mermaid
graph LR
    BEGIN["Start Here"] --> NP["NumPy<br/>arrays & broadcasting"]
    NP --> PD["Pandas<br/>DataFrame wrangling"]
    PD --> VZ["Visualization<br/>explore & present"]
    VZ --> SKL["scikit-learn<br/>Classical ML (AI-ML/...)"]
    SKL --> DL["Deep Learning<br/>(AI-ML/Deep-Learning)"]
```

## Cross-Domain Links

- [[Pandas/_MOC|Pandas]] → [[../../Web-Dev/Programming/Python Deep Dive|Python]], [[System-Design/Databases/Database Indexing Deep Dive|Database Indexing]], [[System-Design/Databases/ETL and Data Pipeline Patterns|ETL]]
- [[NumPy/_MOC|NumPy]] → [[../../Web-Dev/Programming/Python Deep Dive|Python]], [[System-Design/Architecture/Computer Architecture and Organization|Computer Architecture]]
- [[Visualization/_MOC|Visualization]] → [[AI-ML/Deep-Learning/Machine-Learning/_MOC|Machine Learning]]
- [[Pandas/11 End-to-End Pipeline|Data Pipeline]] → [[System-Design/Databases/ETL and Data Pipeline Patterns|ETL Patterns]], [[System-Design/Databases/Data Engineering|Data Engineering]]
- [[Pandas/05 GroupBy Aggregation|GroupBy]] → [[System-Design/Databases/SQL JOIN Operations|SQL JOINs]]
