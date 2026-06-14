---
id: ds-viz-0000-0000-0000-000000000001
title: Visualization — Map of Content
language: markdown
tags: [visualization, matplotlib, seaborn, plotly]
selection: null
isPinned: true
timestamp: 1781700000700
---
# Visualization — Map of Content

Data visualization turns numbers into insights. This folder covers the three major Python visualization libraries: Matplotlib (foundational, customizable), Seaborn (statistical, high-level), and Plotly (interactive, web-ready). Each library serves different needs — use the decision guide below to choose.

**Parent**: [[../_MOC|Data Science]]

## Notes

| Library | File | Best For |
|---------|------|----------|
| Matplotlib | [[Matplotlib]] | Full control, publication-quality, custom layouts |
| Seaborn | [[Seaborn]] | Statistical plots, attractive defaults, data exploration |
| Plotly | [[Plotly]] | Interactive charts, dashboards, web apps |

## Decision Guide

```mermaid
graph TD
    Q["What kind of plot?"]
    Q -->|"Quick exploration"| SEABORN["Seaborn<br/>pandas-friendly, good defaults"]
    Q -->|"Publication / custom"| MPL["Matplotlib<br/>full control, any layout"]
    Q -->|"Interactive / web"| PLOTLY["Plotly<br/>hover, zoom, dashboards"]
    Q -->|"Statistical"| SEABORN
    Q -->|"Time series"| MPL
    Q -->|"Geospatial"| PLOTLY["Plotly + Mapbox"]
```

## Quick Comparison

| Feature | Matplotlib | Seaborn | Plotly |
|---------|-----------|---------|--------|
| API level | Low | High | Medium |
| Default style | Functional | Beautiful | Interactive |
| Interactivity | No | No | Yes (hover, zoom) |
| Statistical plots | Manual | Built-in | Limited |
| Speed | Fast | Fast | Moderate |
| Web export | Static images | Static images | Full HTML/JS |
| 3D plots | Yes | No | Yes |
| Map support | Limited | No | Yes (Mapbox) |
| pandas integration | `.plot()` | `.plot()` + `sns.*` | `plotly.express` |
