---
id: a1b2c3d4-1187-4000-8000-000000000187
title: Disaster Recovery Planning
language: markdown
tags: [devops, monitoring, disaster-recovery]
selection: null
isPinned: false
timestamp: 1781500001187
---
# Disaster Recovery Planning

Disaster Recovery (DR) defines how an organization restores IT infrastructure and data after a disruptive event.

## Key Metrics

| Metric | Definition | Target |
|--------|------------|--------|
| RTO (Recovery Time Objective) | Maximum acceptable downtime | 4 hours (critical), 24 hours (standard) |
| RPO (Recovery Point Objective) | Maximum acceptable data loss | 15 minutes (critical), 1 hour (standard) |
| MTD (Maximum Tolerable Downtime) | Total downtime before business failure | 24-72 hours |
| WRT (Work Recovery Time) | Time to validate and resume operations | 1-2 hours |

## DR Strategies

| Strategy | RTO | RPO | Cost | Description |
|----------|-----|-----|------|-------------|
| Backup & Restore | Hours | 24 hours | Low | Back up to S3/Glacier |
| Pilot Light | Minutes | Minutes | Medium | Core services always running |
| Warm Standby | Minutes | Seconds | Medium-High | Scaled-down prod environment |
| Multi-Site Active/Active | Seconds | Seconds | High | Full production in two regions |
| Cold Standby | Days | Hours | Very Low | Infrastructure config saved only |

## AWS Multi-Region Architecture

```
Region A (Primary)                    Region B (DR)
┌──────────────────┐                 ┌──────────────────┐
│  Route53 (DNS)   │──────Health────►│  Route53 (DNS)   │
│  ALB              │                 │  ALB              │
│  ECS/Fargate      │                 │  ECS/Fargate      │
│  RDS Primary      │══Replication══►│  RDS Standby      │
│  ElastiCache      │                 │  ElastiCache      │
│  S3 (primary)     │══Cross-Region═►│  S3 (replica)     │
└──────────────────┘  Replication    └──────────────────┘
```

## Database Replication for DR

```sql
-- PostgreSQL: Streaming replication to DR region
-- Primary: archive_mode = on, archive_command to S3
-- Standby: restore from S3 archive, streaming from primary

-- MySQL: Cross-region replication
CHANGE MASTER TO
    MASTER_HOST='primary.region-a.example.com',
    MASTER_USER='replication',
    MASTER_PASSWORD='secret',
    MASTER_LOG_FILE='mysql-bin.000123',
    MASTER_LOG_POS=456;

-- DynamoDB Global Tables
aws dynamodb create-global-table \
    --global-table-name orders \
    --replication-group RegionNames us-east-1 us-west-2 eu-west-1
```

## DR Runbook

```yaml
incident: us-east-1 region failure
owner: infrastructure-team
severity: critical

steps:
  - id: 1
    description: "Verify region failure"
    command: "aws health describe-events --region us-east-1"
    expected: "Event type: AWS_REGION_UNAVAILABLE"
    timeout: 2m

  - id: 2
    description: "Route53 failover to us-west-2"
    command: |
      aws route53 update-health-check \
        --health-check-id abc123 \
        --disabled
    expected: "DNS cutover complete"
    timeout: 5m

  - id: 3
    description: "Promote RDS read replica in us-west-2"
    command: |
      aws rds promote-read-replica \
        --db-instance-identifier mydb-replica
    expected: "DB instance promoted, new endpoint: mydb.us-west-2.rds.amazonaws.com"
    timeout: 15m

  - id: 4
    description: "Point application to DR database"
    command: "Update ECS task definition with new DB endpoint"
    timeout: 5m

  - id: 5
    description: "Verify application health in DR region"
    command: "curl -f https://dr.example.com/health"
    expected: "HTTP 200 OK"
    timeout: 2m

  - id: 6
    description: "Notify stakeholders"
    action: "pagerduty trigger, slack announce, email to leadership"
```

## Backup Strategy

```yaml
backup_policy:
  database:
    type: pg_dump
    schedule: "0 */6 * * *"     # Every 6 hours
    retention: 30 days
    destination: s3://backups/db/
    encryption: AES-256

  files:
    type: s3-sync
    schedule: "0 */1 * * *"     # Every hour
    destination: s3://backups/files/
    encryption: SSE-S3

  infrastructure:
    type: terraform-state
    schedule: continuous         # Every apply
    destination: s3://terraform-state/
    versioning: true

  logs:
    type: cloudwatch-export
    schedule: "0 0 * * *"       # Daily
    destination: s3://backups/logs/
    retention: 90 days
```

## Testing the DR Plan

```yaml
# Quarterly DR testing
test_schedule:
  - quarter: Q1
    type: tabletop            # Walk through runbook
    scope: all services
  - quarter: Q2
    type: partial             # Test non-production failover
    scope: tier-3 services
  - quarter: Q3
    type: full                # Full failover of production traffic
    scope: tier-1 services
    duration: 4 hours
  - quarter: Q4
    type: chaos               # Inject failures, test unexpected scenarios
    scope: random services
```

## DR Checklist

```
□ RTO and RPO defined for each service tier
□ Backup strategy documented and automated
□ Replication configured for all stateful services
□ Runbook created and version-controlled
□ DNS failover configured (Route53, CloudFlare)
□ DR site infrastructure provisioned (scaled down)
□ Encryption keys available in DR region
□ IAM roles and policies synced
□ Monitoring and alerting configured for DR region
□ DR plan tested at least annually
□ Test results documented with improvement actions
```

## Common DR Mistakes

| Mistake | Fix |
|---------|-----|
| No DR testing | Schedule quarterly tests |
| Untested backups | Monthly restore validation |
| Manual failover | Automate critical steps |
| Missing DNS TTL reduction | Lower TTL before failover |
| No capacity in DR region | Test scaling in DR |
| Stale runbook | Version control + regular review |
| No communication plan | Define notification tree |
