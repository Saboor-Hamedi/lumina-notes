---
id: a1b2c3d4-1029-4000-8000-000000000029
title: Serverless Computing
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001029
---
# Serverless Computing

Serverless lets you run code without provisioning or managing servers. You pay only for compute time consumed.

## FaaS vs BaaS

| Model | What You Get | Example |
|-------|-------------|---------|
| FaaS (Function as a Service) | Run code on demand | AWS Lambda, Cloud Functions |
| BaaS (Backend as a Service) | Managed backend services | Firebase, Supabase, Auth0 |

## Key Characteristics

| Feature | Serverless | Traditional |
|---------|------------|-------------|
| Scaling | Automatic, per-request | Manual or auto-scaling groups |
| Billing | Per-invocation + duration | Per-hour instance cost |
| Cold starts | Yes (first invocation) | No (always on) |
| Execution limit | Timeout (usually 15 min max) | No limit |
| State | Stateless by design | Can store state on disk |
| Deployment | Zip/container upload | Server provisioning |

## Cold Starts

| Runtime | Typical Cold Start |
|---------|-------------------|
| Python/Node | 200-500ms |
| Java/C# | 1-5s (heavier runtime) |
| Custom Runtime | 2-10s |
| Provisioned Concurrency | 0ms (pre-warmed) |

**Mitigations**: Keep functions warm (ping), use provisioned concurrency, minimize dependencies.

## Serverless Patterns

### API Backend

```
API Gateway → Lambda → DynamoDB
```

### Event Processing

```
S3 Upload → Lambda → Process → SQS → Lambda → Database
```

### Scheduled Tasks

```
CloudWatch Timer → Lambda → Cleanup/Report
```

## Frameworks

| Framework | Description |
|-----------|-------------|
| Serverless Framework | Multi-cloud (AWS, GCP, Azure) |
| AWS SAM | AWS-native with CloudFormation |
| SST | TypeScript-first, full-stack |
| AWS CDK | Infrastructure as code (TypeScript/Python) |
| Chalice | Python microframework for AWS Lambda |

## Lambda Example

```python
import json

def handler(event, context):
    body = json.loads(event["body"])
    return {
        "statusCode": 200,
        "headers": {"Content-Type": "application/json"},
        "body": json.dumps({"result": body["name"]})
    }
```

## Best Practices

- Keep functions small and single-purpose
- Minimize deployment package size
- Use environment variables for configuration
- Implement idempotency for retries
- Set appropriate timeouts and memory
- Externalize state to databases/caches
- Use observability (X-Ray, CloudWatch)
- Handle cold start gracefully (connection pooling outside handler)

## Limitations

- Max execution time (15 min per Lambda invocation)
- Max payload size (6 MB for API Gateway)
- No persistent local storage (/tmp limited to 512 MB-10 GB)
- Vendor lock-in concerns
- Difficult to debug locally
- Not suitable for low-latency or long-running workloads

**Links**: [[Cloud Computing]] | [[Docker Containers]] | [[Kubernetes Basics]] | [[Infrastructure as Code]] | [[CI CD Pipelines]]
