## Deployment Procedures

### Initial Infrastructure Setup

1. **Configure your environment**:
```bash
# Copy template and fill in your values
cp infra/prod.tfvars.template infra/prod.tfvars
# Edit infra/prod.tfvars with your project details
```

2. **Bootstrap GCS backend** (one-time):
```bash
GCP_PROJECT_ID=your-project-id BUCKET=your-tf-state-bucket ./scripts/bootstrap-tf-state.sh
```

3. **Initialize OpenTofu**:
```bash
cd infra
tofu init -backend-config="bucket=your-tf-state-bucket" -backend-config="prefix=your-project/infra"
```

4. **Apply infrastructure**:
```bash
tofu apply -var-file="prod.tfvars"
```

### Administrative Operations

Use the dedicated admin service account for organization-level tasks:

```bash
# Organization policy management
gcloud resource-manager org-policies set-policy policy.yaml \
  --organization={ORGANIZATION_ID} \
  --impersonate-service-account=infrastructure-admin@{GCP_PROJECT_ID}.iam.gserviceaccount.com

# DNS management
gcloud dns record-sets transaction start \
  --zone=boneleve-blog \
  --project={GCP_PROJECT_ID} \
  --impersonate-service-account=infrastructure-admin@{GCP_PROJECT_ID}.iam.gserviceaccount.com
```
