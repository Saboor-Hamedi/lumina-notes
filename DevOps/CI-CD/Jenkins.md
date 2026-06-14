---
id: a1b2c3d4-1097-4000-8000-000000000097
title: Jenkins
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001097
---
# Jenkins

Jenkins is an open-source automation server for CI/CD. It orchestrates build, test, and deployment pipelines through its master-agent architecture and extensive plugin ecosystem.

## Architecture

```
Jenkins Master (scheduler, UI, API)
    │
    ├── Agent 1 (Linux)  ←─ Build Java project
    ├── Agent 2 (Windows) ←─ Build .NET project
    ├── Agent 3 (macOS)  ←─ Build iOS app
    └── Agent 4 (Docker) ←─ Ephemeral build containers
```

| Component | Role |
|-----------|------|
| Master | Manages jobs, schedules builds, serves UI |
| Agent/Node | Executes build jobs (can be many) |
| Executor | Slot for running a build on an agent |
| Job/Project | Configured automation task |
| Pipeline | CI/CD workflow as code |

## Declarative Pipeline

```groovy
pipeline {
    agent any

    triggers {
        githubPush()
        cron('H */4 * * *')  // every 4 hours
    }

    environment {
        APP_NAME = 'my-service'
        DOCKER_REGISTRY = 'registry.example.com'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            parallel {
                stage('Unit') {
                    steps { sh 'npm test' }
                }
                stage('Lint') {
                    steps { sh 'npm run lint' }
                }
                stage('Security') {
                    steps { sh 'npm audit' }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
                sh 'docker build -t ${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh 'docker push ${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}'
                sh 'kubectl set image deployment/${APP_NAME} app=${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}'
            }
        }
    }

    post {
        failure {
            slackSend(
                channel: '#ci-alerts',
                message: "Build ${BUILD_NUMBER} failed: ${BUILD_URL}"
            )
        }
        success {
            slackSend(
                channel: '#ci',
                message: "Build ${BUILD_NUMBER} succeeded"
            )
        }
    }
}
```

## Shared Libraries

```groovy
// vars/buildAndDeploy.groovy
def call(String project, String env) {
    pipeline {
        agent any
        stages {
            stage('Build') {
                steps { sh "docker build -t ${project} ." }
            }
            stage('Deploy') {
                steps { sh "kubectl apply -f deploy/${env}/" }
            }
        }
    }
}

// Jenkinsfile — use shared library
@Library('my-pipeline-lib') _
buildAndDeploy('my-service', 'staging')
```

## Key Plugins

| Plugin | Purpose |
|--------|---------|
| Git | Source control integration |
| Docker Pipeline | Build/push/run Docker containers |
| Kubernetes | Dynamic agents on K8s |
| Blue Ocean | Modern pipeline UI |
| Pipeline | Jenkinsfile-based pipelines |
| Credentials Binding | Secrets management |
| Slack / Email | Notifications |
| SonarQube | Code quality analysis |
| Artifactory/Nexus | Artifact storage |

## Jenkinsfile vs Freestyle

| Aspect | Jenkinsfile (Pipeline) | Freestyle Job |
|--------|----------------------|---------------|
| Version control | Yes (code = pipeline) | No (config in Jenkins) |
| Complexity | Handles complex workflows | Simple only |
| Reusability | Shared libraries | Copy job |
| UI editing | Text editor | Web form |
| Best for | All new projects | Legacy/trivial jobs |

## Master-Agent Security

- Agents should be isolated (containers or VMs)
- Use SSH or JNLP agents (not Java Web Start)
- Restrict agent permissions (run as non-root)
- Use credentials binding, never plaintext secrets

```groovy
withCredentials([
    usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')
]) {
    sh 'docker login -u $USER -p $PASS'
}
```

## Best Practices

- Use Jenkinsfile in repo (Pipeline as Code)
- Keep agents ephemeral (Docker or Kubernetes)
- Separate master from build workload
- Regular plugin updates (security)
- Use `when` conditions for environment-specific stages
- Archive artifacts and test results for traceability

**Links**: [[CI CD Pipelines]] | [[GitHub Actions]] | [[Docker Containers]] | [[Kubernetes Basics]] | [[Developer Workflow Automation]]
