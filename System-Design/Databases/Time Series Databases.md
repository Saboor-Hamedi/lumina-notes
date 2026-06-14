---
id: new-017-0000-0000-0000-000000000017
title: Time Series Databases
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781600000017
---
# Time Series Databases

**Links**: [[Database Engines Compared]] | [[Monitoring and Observability]] | [[Cloud Computing]] | [[Database Sharding]] | [[Data Serialization]]

## What is a Time Series Database?

A time series database (TSDB) is optimized for storing and querying time-stamped data points — metrics, sensor readings, logs, and financial data.

## Key Characteristics

| Feature | Why It Matters |
|---------|----------------|
| **Append-heavy writes** | Data arrives continuously, rarely updated |
| **Time-based queries** | Range scans by time, downsampling |
| **Retention policies** | Auto-expire old data |
| **Downsampling** | Roll up old data to lower resolution |
| **High cardinality** | Billions of unique metric series |

## TSDB vs Relational

| Aspect | PostgreSQL | InfluxDB / TimescaleDB |
|--------|------------|------------------------|
| Write rate | Thousands/sec | Millions/sec |
| Data model | Tables + rows | Measurements + tags + fields |
| Compression | General | Time-specific (delta-of-delta, run-length) |
| Retention | Manual | Automatic policies |
| Downsampling | Manual | Continuous aggregates |

## InfluxDB Example

```sql
-- Create measurement
CREATE DATABASE metrics

-- Write data (line protocol)
weather,city=London temperature=18.5,humidity=72 1700000000
weather,city=Paris  temperature=22.1,humidity=65 1700000001
```

## TimescaleDB (PostgreSQL Extension)

```sql
-- Create hypertable
CREATE TABLE sensor_data (
    time TIMESTAMPTZ NOT NULL,
    device_id TEXT NOT NULL,
    temperature DOUBLE PRECISION,
    humidity DOUBLE PRECISION
);

SELECT create_hypertable('sensor_data', 'time');

-- Time-bucket aggregation
SELECT
    time_bucket('1 hour', time) AS hour,
    device_id,
    AVG(temperature) AS avg_temp,
    MAX(temperature) AS max_temp
FROM sensor_data
WHERE time > NOW() - INTERVAL '7 days'
GROUP BY hour, device_id;
```

## When to Use a TSDB

- Application and infrastructure monitoring
- IoT sensor data
- Financial tick data (stock prices)
- Real-time analytics dashboards
- DevOps observability (metrics + traces)

**Next**: [[Graph Databases]] — Model connected data