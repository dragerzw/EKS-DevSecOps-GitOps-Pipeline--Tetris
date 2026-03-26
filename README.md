# 🚀 Enterprise-Grade DevSecOps & GitOps EKS Project

![DevSecOps](https://img.shields.io/badge/DevSecOps-Mastery-brightgreen)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-Automation-blue)
![SonarCloud](https://img.shields.io/badge/SonarCloud-Code%20Quality-orange)
![Terraform](https://img.shields.io/badge/Terraform-Infrastructure%20as%20Code-9cf)
![ArgoCD](https://img.shields.io/badge/ArgoCD-GitOps%20CD-blue)
![Trivy & OWASP](https://img.shields.io/badge/Security-Trivy%20%7C%20OWASP-red)
![AWS EKS](https://img.shields.io/badge/AWS-Elastic%20Kubernetes%20Service-FF9900)

Welcome to a professional-grade, end-to-end DevSecOps automation project! This repository demonstrates a high-availability, secure deployment of a containerized Tetris application onto a dynamically-provisioned AWS EKS cluster, managed strictly through GitOps orchestration.

## 🏗️ Architecture & Lifecycle

This project implements a **True DevSecOps Lifecycle**, ensuring security and performance at every stage of the pipeline:

1.  **Infrastructure Orchestration (Terraform)**: Automated provisioning of a custom AWS VPC network and EKS Control Plane.
2.  **Continuous Security & CI (GitHub Actions)**: Every commit undergoes static analysis, dependency scanning, and multi-layer container security checks.
3.  **GitOps Continuous Delivery (ArgoCD)**: An in-cluster GitOps controller reconciles the desired state from GitHub directly to the live environment.

---

## 🛠️ Technical Skills & Tooling Matrix

| Category | Tool | Implementation |
| :--- | :--- | :--- |
| **Cloud Infrastructure** | **AWS (EKS, VPC, IAM, S3)** | Managed Kubernetes, networking, and state persistence |
| **IaC / Automation** | **Terraform** | HashiCorp HCL for infrastructure-as-code |
| **CI / CD Orchestration** | **GitHub Actions** | Automated, secure multi-stage deployment pipelines |
| **Code Quality (SAST)** | **SonarCloud** | Automated static code analysis & quality gates |
| **SCA Security** | **OWASP Dependency-Check** | Tracking vulnerable dependencies in open-source libraries |
| **Container Security** | **Trivy** | Scanning for OS-level and application vulnerabilities |
| **Containerization** | **Docker** | Multi-stage builds and image registry management |
| **GitOps Delivery** | **ArgoCD** | Automated, declarative cluster state synchronization |
| **Monitoring / Ops** | **AWS CloudWatch & Logs** | Essential for cluster and application health |

---

## 🚀 Getting Started: Deployment Guide

### 1. Prerequisites
*   An active **AWS Account** with IAM Administrator access.
*   **Docker Hub** account for image hosting.
*   **SonarCloud** organization for automated quality analysis.
*   **AWS CLI** & **kubectl** installed on your local machine.

### 2. Configure GitHub Secrets
Add the following secrets to your GitHub repository (**Settings > Secrets and variables > Actions**):

| Secret Name | Purpose |
| :--- | :--- |
| `AWS_ACCESS_KEY_ID` | Programmatic IAM credentials for Terraform |
| `AWS_SECRET_ACCESS_KEY` | Secret access key for AWS authentication |
| `SONAR_TOKEN` | Authentication for SonarCloud analysis |
| `DOCKER_USERNAME` | Docker Hub username for `docker push` |
| `DOCKER_PASSWORD` | Docker Hub access token |
| `GH_PAT_TOKEN` | GitHub Personal Access Token (with `repo` & `workflow` scopes) |

### 3. Execution Pipeline

#### Phase 1: Infrastructure Provisioning
Simply push any change to the `EKS-TF/` folder or run the **Terraform EKS Deployment** workflow manually.
*   **Outcome**: Dynamically scaffolds a VPC, Internet Gateway, Subnets, and an EKS Cluster.

#### Phase 2: Application DevSecOps
Push any update to the application source code.
*   **Outcome**: Triggers security scans, builds the Docker image, pushes to the registry, and updates the Kubernetes manifests automatically.

#### Phase 3: GitOps Bootstrapping
Install the ArgoCD controller and apply the application link:
```bash
# Connect to cluster
aws eks update-kubeconfig --region us-east-1 --name Tetris-EKS-Cluster

# Bootstrap ArgoCD
kubectl create namespace argocd
kubectl apply --server-side --force-conflicts -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Create declarative GitOps application link
kubectl apply -f Manifest-file/argocd-tetris-app.yaml
```

---

## 🧠 Challenges & Engineering Insights (Resume-Boosting)

Overcoming these high-level technical hurdles demonstrated advanced DevSecOps engineering expertise:

*   **Idempotent Infrastructure Architecture**: Refactored the Terraform module to migrate from static `data` source lookups to fully dynamic `resource` generation. This resolved "circular dependencies" and ensured a zero-touch, standalone VPC/EKS spin-up from a clean AWS account.
*   **Fine-Grained IAM & GitHub Security Convergence**: Architected a secure cross-platform authentication flow. Resolved 403 API permission escalations by implementing precisely-scoped GitHub Personal Access Tokens (PATs) that allow the CI runner to safely update repository manifests while maintaining the **Principle of Least Privilege**.
*   **Scaling Kubernetes Control Planes**: Addressed Kubernetes API manifest size limitations during the bootstrapping of complex GitOps controllers. Leveraged **Server-Side Apply** strategies and conflict-resolution flags to safely install large Custom Resource Definitions (CRDs) for ArgoCD.
*   **CI Pipeline Resilience & Debugging**: Identified and remediated broken third-party action dependencies (OWASP Dependency-Check SHA-pinning failures). Modularized the pipeline to handle stale upstream references without compromising security thresholds.

---

## 📝 Resume-Ready Project Summary

Use the following bullet points for your **Projects** section:

*   **Architected and Automated Infrastructure as Code (IaC)**: Engineered a self-contained, idempotent **Terraform** module to provision a production-grade AWS environment including VPC, Internet Gateway, Subnets, and an EKS Cluster from scratch.
*   **Orchestrated High-Performance CI/CD Pipelines**: Developed a dual-path automation engine in **GitHub Actions** for both infrastructure deployment and application security, achieving a zero-touch "push-to-deploy" workflow.
*   **Implemented a Multi-Layer Security "Safe Belt"**: Integrated **SonarCloud (SAST)** for static analysis, **OWASP Dependency-Check (SCA)** for third-party vulnerability detection, and **Trivy** for high-precision filesystem and Docker image scanning.
*   **Pioneeered GitOps Continuous Delivery**: Bootstrapped **ArgoCD** as the in-cluster GitOps controller, managing declarative application synchronization and automated self-healing of the EKS environment.
*   **Resolved Advanced Kubernetes Orchestration Challenges**: Leveraged **Server-Side Apply** strategies to manage complex CRD installations and configured fine-grained **GitHub PAT scopes** to safely automate manifest updates while adhering to the **PoLP (Principle of Least Privilege)**.

---

## 💰 Cleanup & Cost Management
To avoid unexpected AWS charges, always decommission your resources when finished:

1.  **Delete the GitOps application**: `kubectl delete -f Manifest-file/argocd-tetris-app.yaml`
2.  **Destroy Infrastructure**: Navigate to `EKS-TF/` and run `terraform destroy -var-file=variables.tfvars`
3.  **Delete S3 Backend (Manual)**: Remove the `dragops-tf-bucket` in S3 to clean up state persistence.

---

## 📄 License
This portfolio project is licensed under the Apache-2.0 license.