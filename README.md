# Kubernetes GitOps CI/CD Pipeline

[![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)](https://github.com/features/actions)
[![Kubernetes](https://img.shields.io/badge/Orchestration-Kubernetes-326CE5?logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Docker](https://img.shields.io/badge/Registry-Docker%20Hub-2496ED?logo=docker&logoColor=white)](https://hub.docker.com/)
[![Node.js](https://img.shields.io/badge/Runtime-Node.js-339933?logo=nodedotjs&logoColor=white)](https://nodejs.org/)

An automated continuous integration and continuous deployment (CI/CD) pipeline demonstrating modern GitOps workflows: test automation on pull requests, semantic versioned Docker container synthesis, registry publishing, and automated rolling deployments to a Kubernetes (AWS EKS / K3s) cluster.

---

## Pipeline Architecture

```
  Feature Branch               Main Branch                  Release Tag (v*)
  ──────────────               ───────────                  ────────────────
        │                           │                              │
   [ Pull Request ]                 │                              │
        │                           │                              │
        ▼                           ▼                              ▼
  ┌─────────────┐             ┌─────────────┐               ┌─────────────┐
  │  Workflow   │             │   Merged    │               │  Workflow   │
  │ 'integrate' │             │  to 'main'  │               │ 'delivery'  │
  └──────┬──────┘             └─────────────┘               └──────┬──────┘
         │                                                         │
   Runs Vitest / Unit Tests                                  Docker Build & Push
   (Blocks merge on failure)                                 (Tagged semver + latest)
                                                                   │
                                                                   ▼
                                                            ┌─────────────┐
                                                            │  Workflow   │
                                                            │  'deploy'   │
                                                            └──────┬──────┘
                                                                   │
                                                            Kubectl Apply Manifests
                                                            (Rolling update to K8s)
```

---

## Workflows Breakdown

1. **Continuous Integration (`integrate.yml`)**:
   - Triggers on every Pull Request targeting the `main` branch.
   - Installs dependencies and runs automated test suites.
   - Merging is gated until all tests pass cleanly.

2. **Continuous Delivery (`delivery.yml`)**:
   - Triggers upon pushing semantic version tags (e.g., `v1.0.0`).
   - Builds optimized, multi-stage Docker images.
   - Authenticates and publishes container artifacts to Docker Hub with both versioned and `latest` tags.

3. **Continuous Deployment (`deploy.yml`)**:
   - Automatically triggered upon successful completion of the delivery workflow.
   - Authenticates with the Kubernetes cluster via kubeconfig credentials.
   - Applies declarative manifests (`k8s/deployment.yaml`, `k8s/service.yaml`) executing zero-downtime rolling updates.

---

## Local Development & Testing

### Prerequisites
- Node.js 20+
- Docker Engine

### Run Locally
```bash
npm install
npm test
npm start
```

### Build & Run Container
```bash
docker build -t k8s-service-api .
docker run -p 3000:3000 k8s-service-api
```

---

## Kubernetes Manifests

* `k8s/deployment.yaml`: Defines replica sets, container specs, resource requests/limits, and readiness/liveness probes.
* `k8s/service.yaml`: Configures the networking service (NodePort/LoadBalancer) routing traffic to pod endpoints.

---

## Author
**Luis Alberto González Escamilla**  
Software & Systems Engineer · AI Agents & Cloud Solutions
