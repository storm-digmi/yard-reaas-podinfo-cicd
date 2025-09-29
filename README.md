# yard-reaas-podinfo-cicd
# Application Pipeline – Podinfo Lambda

This repository contains the **GitHub Actions pipeline** that builds, pushes, and deploys the Podinfo application as a Lambda container image.

---

## Tools
- **GitHub Actions** for CI/CD.  
- **Docker Buildx** + **Skopeo** for building and pushing multi-arch container images.  
- **AWS CLI** for Lambda & CodeDeploy updates.  
- **Go** for running Podinfo unit tests.  

---

## Pipeline Overview
The application pipeline performs the following:  

1. **Tests**  
   - Runs `go test ./...` on the Podinfo repo to ensure code is valid.  

2. **Build & Push**  
   - Builds **multi-arch (amd64 + arm64)** image using Buildx.  
   - Exports as **OCI tarball**.  
   - Pushes to **Amazon ECR** via `skopeo copy`.  

3. **Deploy**  
   - Extracts the `amd64` digest from the manifest index (Lambda requirement).  
   - Updates Lambda function code with the new image.  
   - Publishes a new version.  
   - Creates a **CodeDeploy deployment** → blue/green rollout on alias `prod`.  

4. **Verify**  
   - Calls the API Gateway endpoint `/healthz`.  
   - Pipeline fails if response != 200.  

5. **Secrets Demo**  
   - Reads dummy secret from **Secrets Manager** (ARN injected in env vars).  
   - Writes it to Lambda’s CloudWatch log group → masking ensures the raw value never appears in logs.  

---

## Trigger
- **On push** to `main`, but only if files under `dockerfile/podinfo/**` are modified.  
- Can also be run manually via `workflow_dispatch`.  

---

## Cost Reference
Pipeline itself is free under GitHub Actions quota.  
AWS costs include:  
- Lambda invocations (pay-per-request).  
- ECR storage (container images).  
- CodeDeploy executions.  
- CloudWatch logs/alarms/dashboards.  
- API Gateway requests.  
See [AWS Pricing – EU (Frankfurt)](https://aws.amazon.com/de/pricing/) for details.  

