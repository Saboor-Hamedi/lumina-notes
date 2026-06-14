---
id: a1b2c3d4-1098-4000-8000-000000000098
title: GitLab CI
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001098
---
# GitLab CI

GitLab CI is GitLab's built-in CI/CD system. Pipelines are defined in YAML and run on GitLab runners, tightly integrated with the GitLab ecosystem.

## Architecture

```
GitLab Repository → Pipeline Trigger (push/MR)
                        ↓
               GitLab Server (coordinator)
                        ↓
               GitLab Runner (executor)
                        ↓
                 Docker / K8s / Shell / SSH
```

## .gitlab-ci.yml

```yaml
stages:
  - test
  - build
  - deploy

variables:
  APP_NAME: my-service
  DOCKER_DRIVER: overlay2

cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
    - .npm/

before_script:
  - node --version
  - npm ci

test:
  stage: test
  script:
    - npm run lint
    - npm run test:coverage
  coverage: '/All files[|][^|]+[|]\s+([\d.]+)/'
  artifacts:
    paths:
      - coverage/
    expire_in: 30 days

build:
  stage: build
  script:
    - npm run build
    - docker build -t ${CI_REGISTRY_IMAGE}:${CI_COMMIT_SHORT_SHA} .
    - docker push ${CI_REGISTRY_IMAGE}:${CI_COMMIT_SHORT_SHA}
  only:
    - main
```

## Environments & Deploy

```yaml
deploy_staging:
  stage: deploy
  script:
    - kubectl set image deployment/${APP_NAME} app=${CI_REGISTRY_IMAGE}:${CI_COMMIT_SHORT_SHA}
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - main

deploy_production:
  stage: deploy
  script:
    - kubectl set image deployment/${APP_NAME} app=${CI_REGISTRY_IMAGE}:${CI_COMMIT_SHORT_SHA}
  environment:
    name: production
    url: https://example.com
  when: manual  # requires approval
  only:
    - tags
```

## Key Features

| Feature | Description |
|---------|-------------|
| Auto DevOps | Pre-built CI template for common apps |
| Review Apps | Ephemeral environments per MR |
| CI/CD for Kubernetes | Built-in K8s integration |
| Parent/Child Pipelines | Dynamic pipeline generation |
| DAG Pipelines | Define execution dependencies |
| Runners | Docker, K8s, SSH, Shell |
| Registry | Built-in Docker container registry |

## Rules vs Only/Except

```yaml
# Modern approach: rules (replaces only/except)
test:
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_COMMIT_BRANCH == "main"
    - if: $CI_COMMIT_TAG
    - when: never  # skip in other cases

# Conditional execution
deploy:
  rules:
    - if: $CI_COMMIT_TAG
      when: manual
    - if: $CI_COMMIT_BRANCH == "main"
```

## Matrix Jobs

```yaml
test:
  parallel:
    matrix:
      - NODE_VERSION: ["18", "20", "22"]
        DB: ["postgres", "mysql"]
  script:
    - nvm use ${NODE_VERSION}
    - npm test -- --db ${DB}
```

## DAG (Directed Acyclic Graph)

```yaml
build_a:
  stage: build
  script: make build_a
  needs: []  # no dependencies

build_b:
  stage: build
  script: make build_b
  needs: []

test:
  stage: test
  script: make test
  needs: [build_a, build_b]  # runs after both
```

## GitLab Runner

```bash
# Register a runner
gitlab-runner register \
  --url https://gitlab.com \
  --token glrt-xxx \
  --executor docker \
  --docker-image alpine:latest

# Executors
# docker, kubernetes, shell, ssh, virtualbox, parallels
```

## vs GitHub Actions

| Feature | GitLab CI | GitHub Actions |
|---------|-----------|----------------|
| Pipeline config | .gitlab-ci.yml | .github/workflows/*.yml |
| Built-in registry | Yes | Yes (GitHub Container Registry) |
| Self-hosted runners | Yes | Yes |
| Environments | Native | Native |
| Review apps | Yes | Preview environments |
| Open source | Yes | Yes (with limits) |

**Links**: [[CI CD Pipelines]] | [[GitHub Actions]] | [[Docker Containers]] | [[Kubernetes Basics]] | [[Developer Workflow Automation]]
