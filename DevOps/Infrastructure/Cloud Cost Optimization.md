---
id: a1b2c3d4-1188-4000-8000-000000000188
title: Cloud Cost Optimization
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001188
---
# Cloud Cost Optimization (FinOps)

FinOps brings financial accountability to cloud spending through collaboration between engineering, finance, and product teams.

## Cloud Cost Categories

| Category | Typical % | Optimization Levers |
|----------|-----------|-------------------|
| Compute | 40-60% | Right-sizing, reserved instances, spot, autoscaling |
| Storage | 15-25% | Lifecycle policies, tiering, compression |
| Network | 5-10% | Data transfer optimization, CDN |
| Database | 10-20% | Reserved instances, serverless, right-sizing |
| Managed Services | 5-15% | Reserved capacity, usage optimization |

## Right-Sizing

```bash
# Check for over-provisioned resources
# CPU < 20% most of the time → downgrade instance type
# Memory < 30% most of the time → downgrade
# Network < 10% → downgrade

# AWS Compute Optimizer
aws compute-optimizer get-ec2-instance-recommendations --instance-arn arn:aws:ec2:...

# Right-sizing schedule:
# 1. Identify over-provisioned instances (CloudWatch, Compute Optimizer)
# 2. Verify with load testing
# 3. Schedule resize during maintenance window
# 4. Monitor post-resize metrics
```

## Reserved Instances vs Savings Plans

```python
# Purchase recommendations based on 3-month lookback
def recommend_commitments(usage_hours_by_instance):
    """Analyze past usage and recommend RI/SP purchase."""
    recommendations = []

    for instance_type, hours in usage_hours_by_instance.items():
        avg_hours = sum(hours) / len(hours)

        if avg_hours > 0.7 * 24 * 30:  # > 70% utilization for 30 days
            recommendations.append({
                "instance_type": instance_type,
                "recommended": "Standard RI (1-year)",
                "expected_savings": "40%",
            })
        elif avg_hours > 0.3 * 24 * 30:
            recommendations.append({
                "instance_type": instance_type,
                "recommended": "Convertible RI (3-year)",
                "expected_savings": "30%",
            })
        else:
            # Low utilization — keep on-demand
            continue

    return recommendations
```

| Commitment | Discount | Flexibility | Term |
|------------|----------|-------------|------|
| On-demand | 0% | Maximum | None |
| Reserved Instance (Standard) | 40-60% | Low (fixed type) | 1-3 year |
| Reserved Instance (Convertible) | 30-50% | Medium (change family) | 1-3 year |
| Savings Plan (Compute) | 30-60% | High (any instance) | 1-3 year |
| Spot Instances | 60-90% | Low (can be terminated) | None |

## Spot Instances

```yaml
# Spot instance strategy for fault-tolerant workloads
workloads:
  - name: batch-processing
    strategy: spot
    fallback: on-demand
    max_spot_price: 0.10  # $/hour

  - name: kubernetes-workers
    strategy: spot-mixed
    spot_percentage: 70
    instance_pools: 5  # Diverse instance types reduce interruption

  - name: database
    strategy: on-demand-only  # Critical stateful workloads
```

## Storage Optimization

```yaml
# S3 lifecycle policy
Rules:
  - prefix: /logs/
    transitions:
      - days: 30
        storage_class: STANDARD_IA    # Infrequent Access
      - days: 90
        storage_class: GLACIER         # Archive
      - days: 365
        storage_class: DEEP_ARCHIVE   # Deep Archive
    expiration:
      days: 730                       # Delete after 2 years

# EBS optimization
# Delete unattached volumes
# Use gp3 instead of gp2 (20% cheaper, baseline performance)
# Snapshot old volumes before deletion
```

## Autoscaling

```yaml
# Target tracking scaling policy
resource: ecs-service/my-api
policies:
  - metric: CPUUtilization
    target: 60
    scale_in: true
    cooldown: 300

  - metric: MemoryUtilization
    target: 70
    scale_in: true
    cooldown: 300

# Scheduled scaling (known traffic patterns)
scheduled_actions:
  - name: scale-up-business-hours
    schedule: "0 8 * * 1-5"     # Weekdays 8 AM
    min_capacity: 10
    max_capacity: 50

  - name: scale-down-night
    schedule: "0 20 * * 1-5"    # Weekdays 8 PM
    min_capacity: 2
    max_capacity: 10
```

## Data Transfer Costs

```yaml
# Data transfer is often overlooked but can be significant
# Strategies:
#   - Use CDN for static assets (CloudFront, CloudFlare)
#   - Keep services in same AZ (free inter-AZ data transfer)
#   - Use VPC endpoints instead of NAT gateway
#   - Compress data before transfer
#   - Cache at edge (CloudFront, Lambda@Edge)

# AWS data transfer costs:
#   Internet egress: $0.09/GB (varies by region)
#   Cross-region: $0.01-0.09/GB
#   Same AZ: Free
#   VPC endpoint (S3/Gateway): Free
#   NAT Gateway: $0.045/hour + $0.045/GB processed
```

## Monitoring and Budgets

```yaml
# Budget alerts
Budgets:
  - name: monthly-infra
    amount: 50000
    type: cost
    alerts:
      - threshold: 80
        actions: [email-team, slack-notification]
      - threshold: 100
        actions: [email-manager, pagerduty]

  - name: monthly-dev
    amount: 5000
    type: cost
    alerts:
      - threshold: 90
        actions: [email-team]

# Cost allocation tags
required_tags:
  - key: cost-center
    values: [engineering, marketing, data-science]
  - key: environment
    values: [production, staging, development]
  - key: project
    values: [checkout, search, recommendations]
```

## Tagging Strategy

```yaml
# Comprehensive tagging
tags:
  - key: Environment
    values: [Production, Staging, Development, Test]
  - key: CostCenter
    values: [Engineering, Marketing, DataScience, Infrastructure]
  - key: Project
    values: [CheckoutFlow, SearchPlatform, MLPipeline]
  - key: Owner
    values: [team-name]
  - key: CreatedBy
    values: [terraform, cloudformation, manual]
  - key: AutoShutdown
    values: ["true", "false"]  # For non-production resources
```

## FinOps Maturity Model

| Stage | Characteristics | Activities |
|-------|-----------------|------------|
| 1: Crawl | No visibility, reactive | Enable cost allocation tags, set budgets |
| 2: Walk | Weekly cost reviews | Right-sizing, reserved instances |
| 3: Run | Real-time dashboards | Continuous optimization, spot adoption |
| 4: Optimize | Automated optimization | Auto-scaling, rightsizing automation |
| 5: Predict | Predictive analytics | ML-based forecasting, dynamic procurement |
