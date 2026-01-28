# Lab3-Jenkins – End-to-End CI/CD with Jenkins, Docker, and Argo CD

## Overview
This repository demonstrates a complete **CI/CD pipeline** using **Jenkins**, **Docker**, **SonarQube**, and **Argo CD** following **GitOps best practices**.

Jenkins is responsible for **Continuous Integration (CI)**:
- Building and testing the application
- Running static code analysis
- Building and pushing Docker images
- Updating Kubernetes manifests

Argo CD is responsible for **Continuous Delivery (CD)**:
- Monitoring this Git repository
- Automatically deploying updated Kubernetes manifests to the cluster

---

## High-Level Architecture
Developer → GitHub → Jenkins → Docker Hub
↓
Kubernetes Manifests
↓
Argo CD
↓
Kubernetes Cluster

---

## CI/CD Pipeline Flow

### 1. Checkout
- Jenkins clones the `main` branch from GitHub

### 2. Build & Test
- Maven builds the Spring Boot application
- Unit tests are executed

### 3. Static Code Analysis
- SonarQube analyzes code quality and security issues

### 4. Docker Build & Push
- Jenkins builds a Docker image
- Image is tagged with the Jenkins `BUILD_NUMBER`
- Image is pushed to Docker Hub

### 5. Update Kubernetes Deployment
- Jenkins updates the Docker image tag in `kubernetes/deployment.yml`
- Changes are committed and pushed back to GitHub

### 6. Argo CD Deployment (GitOps)
- Argo CD detects the manifest change
- Automatically syncs and deploys the application to the Kubernetes cluster

---

## Repository Structure
.
├── Jenkinsfile
├── Dockerfile
├── pom.xml
├── kubernetes/
│ └── deployment.yml
└── README.md

---

## Jenkins Configuration

### Required Jenkins Credentials

| Credential ID  | Type                     | Description |
|----------------|--------------------------|-------------|
| `github-token` | Secret Text (GitHub PAT) | Push Kubernetes manifest updates |
| `docker-cred`  | Username & Password      | Docker Hub authentication |
| `sonarqube`    | Secret Text              | SonarQube authentication token |

---

## Docker Image Strategy
Docker images are built and pushed using immutable tags:

mohamedhassan22/jenkins-repo:<BUILD_NUMBER>

Each pipeline execution produces a **new image version**, ensuring:
- Traceability
- Easy rollback
- No mutable `latest` tags

---

## Kubernetes Deployment
Kubernetes manifests are stored in:


Jenkins automatically updates the image field:
```yaml
image: mohamedhassan22/jenkins-repo:<BUILD_NUMBER>


Argo CD Integration

Argo CD watches this repository

Path monitored: kubernetes/

Target revision: main

Auto-sync enabled


Benefits

Git as the single source of truth

No manual kubectl apply

Full deployment history in Git

Easy rollback by reverting commits