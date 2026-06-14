---
tags: [data-engineering, reverse-etl, data-mesh, data-fabric]
---

# 06 — Reverse ETL & Data Mesh

## Reverse ETL

```mermaid
flowchart LR
    DW[(Snowflake/BigQuery/Redshift)] --> REV[Reverse ETL: Hightouch/Census]
    REV --> CRM[Salesforce/HubSpot]
    REV --> MKTG[Marketo/Braze]
    REV --> SUPPORT[Zendesk]
    REV --> ADS[Facebook/Google Ads]
```

| Use Case | Source | Destination |
|----------|--------|-------------|
| Customer scoring | Customer 360 table | Salesforce lead score |
| Product recommendations | Purchase history | Braze event triggers |
| Ad audiences | Segment membership | Facebook Custom Audiences |
| Support enrichment | Order history | Zendesk ticket sidebar |
| Sales alerts | KPI changes | Slack notifications |

## Data Mesh vs Data Fabric

| Aspect | Data Mesh | Data Fabric |
|--------|-----------|-------------|
| Philosophy | Organizational (socio-technical) | Technical (architecture) |
| Ownership | Domain teams own their data | Centralized IT governance |
| Architecture | Decentralized domains + shared infra | Virtualized, connected layer |
| Governance | Federated computational governance | Centralized policy management |
| Key principle | "You built it, you run it" | "Connect anywhere, govern centrally" |
| Best for | Large orgs with distinct domains | Hybrid multi-cloud environments |

```mermaid
graph TB
    subgraph "Data Mesh"
        D1[Domain: Marketing] --> DP1[(Data Product)]
        D2[Domain: Sales] --> DP2[(Data Product)]
        D3[Domain: Support] --> DP3[(Data Product)]
        DP1 <--> DP2 <--> DP3
        subgraph "Shared Infrastructure"
            IAM[Global IAM]; CAT[Data Catalog]; COMP[Compute]; STOR[Storage]
        end
    end
    subgraph "Data Fabric"
        VIRT[Virtualization Layer] --> CONS[Consumers]
        POL[Policy Engine] --> VIRT
        GRAPH[Knowledge Graph] --> VIRT
        S1[(DB)] & S2[(DB)] & S3[(S3)] & S4[(API)] --> VIRT
    end
```

**Links**: [[System-Design/Databases/Data Engineering/02 Architecture & Medallion]] | [[System-Design/Databases/Data Engineering/05 Data Quality & Cataloging]] | [[System-Design/Databases/Data Engineering/09 Cost Optimization]]
**See also**: [[Data Warehouse Modeling]], [[Apache Kafka]]
