# EKS DevSecOps & GitOps Pipeline

A secure, automated pipeline for deploying a containerized application to AWS Elastic Kubernetes Service (EKS) using GitOps principles.

---

## Table of Contents

- [Overview](#overview)
- [Technology Stack](#technology-stack)
- [Pipeline Architecture](#pipeline-architecture)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Required Secrets](#required-secrets)
  - [Execution](#execution)
- [Challenges & Resolutions](#challenges--resolutions)
- [Cleanup](#cleanup)
- [License](#license)

---

## Overview

This project implements a full DevSecOps lifecycle around a containerized application, encompassing:

- **CI/CD automation** with integrated container scanning and Kubernetes deployment
- **Security enforcement** at every pipeline stage (SAST, SCA, and container analysis)
- **GitOps-driven delivery** to a production-grade Kubernetes cluster on AWS

---

## Technology Stack

| Category | Tool |
| :--- | :--- |
| Cloud Provider | AWS (EKS, VPC, S3) |
| Infrastructure as Code | Terraform |
| CI/CD Platform | GitHub Actions |
| Static Analysis (SAST) | SonarCloud |
| Software Composition Analysis | OWASP Dependency-Check |
| Container Security | Trivy |
| Containerization | Docker |
| GitOps Controller | ArgoCD |

---

## Pipeline Architecture


![Pipeline Architecture](https://github.com/user-attachments/assets/3be06da7-d92f-411b-b047-2fd225780996)

The pipeline is split into two independent automation streams:

### 1. Infrastructure Provisioning (IaC)

Terraform provisions a dedicated VPC, subnets, and the EKS cluster from scratch, ensuring fully reproducible, dependency-free environment creation.

### 2. Application Lifecycle (CI/CD + GitOps)

GitHub Actions orchestrates the build, security scanning, and image push stages. ArgoCD continuously reconciles the live cluster state against the manifests stored in this repository.


---

## Getting Started

### Prerequisites

- AWS account with appropriate IAM permissions
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) and [kubectl](https://kubernetes.io/docs/tasks/tools/) installed locally
- Docker Hub account for image storage
- SonarCloud account for static code analysis

### Required Secrets

Add the following secrets to your GitHub repository under **Settings → Secrets and variables → Actions**:

| Secret | Description |
| :--- | :--- |
| `AWS_ACCESS_KEY_ID` | AWS IAM access key |
| `AWS_SECRET_ACCESS_KEY` | AWS IAM secret key |
| `SONAR_TOKEN` | SonarCloud authentication token |
| `DOCKER_USERNAME` | Docker Hub username |
| `DOCKER_PASSWORD` | Docker Hub password or access token |
| `GH_PAT_TOKEN` | GitHub PAT with `repo` and `workflow` scopes |

### Execution

**Step 1 — Provision Infrastructure**

Trigger the `terraform.yml` workflow manually, or push changes to the `EKS-TF/` directory to invoke it automatically.

**Step 2 — Build & Scan the Application**

Push changes to the application source. The `cicd.yml` workflow will automatically run SAST, SCA, container scanning, and push the validated image to Docker Hub.

**Step 3 — Bootstrap GitOps with ArgoCD**

Once the cluster is live, configure `kubectl` and deploy ArgoCD:

```bash
# Configure cluster access
aws eks update-kubeconfig --region us-east-1 --name Tetris-EKS-Cluster

# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f \
  https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Apply the application manifest
kubectl apply -f Manifest-file/argocd-tetris-app.yaml
```

ArgoCD will detect the manifest and begin continuous reconciliation against the cluster.

---

## Challenges & Resolutions

### Dynamic Infrastructure Configuration

**Problem:** Terraform modules relied on static data-source lookups, causing circular dependency failures in fresh AWS environments.

**Resolution:** Refactored all modules to use dynamic resource references, resulting in a fully self-contained VPC/EKS deployment with no external dependencies.

---

### Kubernetes API Scaling & CRD Size Limits

**Problem:** ArgoCD's high-density Custom Resource Definitions exceeded manifest size thresholds, causing apply failures.

**Resolution:** Switched to **Server-Side Apply** to offload merge logic to the API server, with targeted manual conflict resolution for conflicting CRD fields.

---

### Automated Workflow Authorization

**Problem:** Cross-platform automated manifest updates were rejected with 403 errors due to insufficient token permissions.

**Resolution:** Replaced broad-scope tokens with fine-grained GitHub PATs scoped strictly to `repo` and `workflow`, enabling secure automated commits within the CD lifecycle.

---

## Cleanup

To decommission all resources and stop incurring AWS costs:

```bash
# 1. Remove the ArgoCD application
kubectl delete -f Manifest-file/argocd-tetris-app.yaml

# 2. Destroy all provisioned infrastructure
cd EKS-TF/
terraform destroy
```

> **Note:** Confirm all AWS resources (EKS nodes, load balancers, NAT gateways) are terminated in the AWS console after `terraform destroy` completes.

---

## License

Licensed under the [Apache-2.0 License](LICENSE).
