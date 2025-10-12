# Hello EKS CI/CD Demo

This repository demonstrates a full pipeline: **Terraform → EKS (AWS)** provisioning, **Docker** containerization, **Kubernetes** manifests, and an automated **GitHub Actions** CI/CD pipeline that builds and deploys the app to EKS on every push to `main`.

## What is included
- Simple Flask "Hello, World" app (app/)
- Dockerfile optimized with multi-stage build (docker/Dockerfile)
- Kubernetes manifests (k8s/deployment.yaml, k8s/service.yaml)
- Terraform to provision VPC & EKS cluster (terraform/)
- GitHub Actions workflow for build → push to ECR → deploy to EKS (.github/workflows/ci-cd.yml)

## Design choices (brief)
- **AWS EKS + ECR** used because Terraform support and GH Actions integrations are well supported.
- **Python Flask** app – tiny, easy to demonstrate deployment.
- **Docker multi-stage** build for smaller images.
- **LoadBalancer Service** for quick public access (simple demo). For production use, consider ALB Ingress and HTTPS via cert-manager.
- **Terraform EKS module** used to keep infra code concise and maintain best-practice defaults.

## Quick start — create infra
1. Install tools: `terraform` (>=1.2), `awscli`, `kubectl`.
2. Configure AWS CLI with credentials that can create VPC, EKS, IAM roles, and ECR:
   ```bash
   aws configure

