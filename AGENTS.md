# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## Common Development Commands

### GCP Deployment Commands
Set required environment variables first:
```bash
export PROJECT_ID={{GCP_PROJECT_ID}}
export GCP_REGION={{GCP_REGION}}
export SERVICE_NAME=meat-dist-data-platform
export REPO=meat-dist-data-platform
```

### Infrastructure Management
Bootstrap Terraform state (one-time):
```bash
PROJECT_ID={{GCP_PROJECT_ID}} GCS_BUCKET={{YOUR_TF_STATE_BUCKET}} ./scripts/bootstrap-tf-state.sh
```

Apply infrastructure:
```bash
cd infra
terraform init -backend-config="bucket={{YOUR_TF_STATE_BUCKET}}" -backend-config="prefix=infra"
terraform apply \
  -var="project_id={{GCP_PROJECT_ID}}" \
  -var="project_number={{GCP_PROJECT_NUMBER}}" \
  -var="pool_id={{GCP_WORKLOAD_IDENTITY_POOL}}" \
  -var="provider_id={{GCP_WORKLOAD_IDENTITY_PROVIDER}}" \
  -var="github_owner=<github_owner>" \
  -var="github_repo={{REPO}}" \
  -var="cloud_run_url={{CLOUD_RUN_SERVICE_URL}}"
```

## Architecture Overview

### Deployment Architecture
- **Cloud Run**: Containerized deployment on Google Cloud Platform
- **Load Balancer**: Global HTTP(S) load balancer for custom domain SSL support
- **GitHub Actions CI/CD**: Automated deployment via Workload Identity Federation
- **Artifact Registry**: Container image storage
- **Infrastructure as Code**: Terraform/OpenTofu for WIF setup and IAM roles

### Infrastructure Components
The `infra/` directory contains Terraform configuration for:
- Workload Identity Pool and Provider for GitHub OIDC authentication
- Service account IAM bindings for deployment permissions
- Required project-level roles: Cloud Run admin, Artifact Registry writer, Load Balancer admin
- Global HTTP(S) Load Balancer with SSL certificates for custom domain support
- DNS zone and records for domain management
- Network Endpoint Group (NEG) connecting load balancer to Cloud Run

## Security Considerations
- Container runs as non-root user
- Uses minimal IAM permissions via dedicated service account
- Secrets managed via environment variables, not baked into images
