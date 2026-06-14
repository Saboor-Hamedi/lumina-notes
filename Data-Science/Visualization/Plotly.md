---
id: ds-viz-0003-0000-0000-000000000003
title: Plotly Interactive Charts
language: markdown
tags: [plotly, visualization, interactive, dash]
selection: null
isPinned: false
timestamp: 1781700000703
---
# Plotly Interactive Charts

**Links**: [[_MOC]] | [[Matplotlib]] | [[Seaborn]]

## Setup

```python
import plotly.express as px
import plotly.graph_objects as go
import pandas as pd
```

## Plotly Express (High-Level API)

```python
df = px.data.gapminder()

# Scatter
px.scatter(df, x='gdpPercap', y='lifeExp', color='continent',
    size='pop', hover_name='country', log_x=True, size_max=60,
    animation_frame='year')

# Line
px.line(df, x='year', y='lifeExp', color='country',
    title='Life Expectancy Over Time')

# Bar
px.bar(df[df['year'] == 2007], x='continent', y='pop',
    color='continent', title='Population by Continent 2007')

# Histogram
px.histogram(df, x='lifeExp', nbins=50, color='continent')
```

## Graph Objects (Low-Level API)

```python
fig = go.Figure()

fig.add_trace(go.Scatter(
    x=df['year'], y=df['lifeExp'],
    mode='lines+markers',
    name='Life Expectancy',
    line=dict(color='blue', width=2),
))

fig.update_layout(
    title='Life Expectancy Over Time',
    xaxis_title='Year',
    yaxis_title='Life Expectancy',
    template='plotly_white',
    hovermode='x unified',
)

fig.show()
```

## Interactive Features

```python
fig = px.scatter(df, x='gdpPercap', y='lifeExp', color='continent')

# Custom hover
fig.update_traces(
    hovertemplate='<b>%{hovertext}</b><br>GDP: %{x:$,.0f}<br>Life: %{y:.1f}'
)

# Range slider
fig.update_xaxes(rangeslider_visible=True)

# Buttons
fig.update_layout(
    updatemenus=[{
        'buttons': [
            {'label': 'Linear', 'method': 'update',
             'args': [{'visible': [True]}, {'xaxis': {'type': 'linear'}}]},
            {'label': 'Log', 'method': 'update',
             'args': [{'visible': [True]}, {'xaxis': {'type': 'log'}}]},
        ],
        'direction': 'down',
    }]
)

# Annotations
fig.add_annotation(
    x=50000, y=80,
    text='Wealthy countries',
    showarrow=True,
    arrowhead=2,
)
```

## Subplots

```python
from plotly.subplots import make_subplots

fig = make_subplots(
    rows=2, cols=2,
    subplot_titles=('Scatter', 'Bar', 'Histogram', 'Box'),
)

fig.add_trace(go.Scatter(x=[1,2,3], y=[4,5,6]), row=1, col=1)
fig.add_trace(go.Bar(x=['A','B','C'], y=[10,20,15]), row=1, col=2)
fig.add_trace(go.Histogram(x=[1,2,2,3,3,3,4]), row=2, col=1)
fig.add_trace(go.Box(y=[1,2,3,4,5,6,7]), row=2, col=2)

fig.update_layout(height=600, showlegend=False)
fig.show()
```

## Dashboards with Dash

```python
import dash
from dash import dcc, html

app = dash.Dash(__name__)

app.layout = html.Div([
    html.H1('Sales Dashboard'),
    dcc.Dropdown(
        id='store-dropdown',
        options=[{'label': s, 'value': s} for s in stores],
        value='S001'
    ),
    dcc.Graph(id='sales-chart'),
])

@app.callback(
    dash.Output('sales-chart', 'figure'),
    dash.Input('store-dropdown', 'value')
)
def update_chart(store):
    filtered = df[df['store'] == store]
    return px.line(filtered, x='date', y='revenue')

if __name__ == '__main__':
    app.run_server(debug=True)
```

## Saving

```python
# HTML (full interactivity)
fig.write_html('chart.html')

# Static images (requires kaleido or orca)
fig.write_image('chart.png', width=1200, height=600)
fig.write_image('chart.pdf')

# JSON (for re-use)
fig.write_json('chart.json')
```
