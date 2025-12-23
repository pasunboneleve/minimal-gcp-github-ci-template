## Deployment Procedures

### Install dependencies

- [direnv](https://direnv.net/)
- [open tofu](https://opentofu.org/)

### Set environment variables

```bash
cp .env.template .env
cp infra/prod.tfvars.template infra/prod.tfvars
```
and fill the details in the these new files. Then run

```bash
direnv allow
```

if you need to reload the environment variables, use

```bash
direnv reload
```

### Initial Infrastructure Setup

2. **Bootstrap GCS backend** (one-time):
```bash
./scripts/bootstrap-tf-state.sh
```

3. **Initialize OpenTofu**:
```bash
cd infra
tofu init -backend-config="bucket=$GCS_BUCKET" -backend-config="prefix=$GCP_PROJECT_ID/infra"
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
