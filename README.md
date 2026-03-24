# Lab M5.06 - Deployment Strategies (Blue/Green)

## Architecture

Two S3 static website buckets serve as deployment targets:
- **Blue** — `deploy-lab-677746514416-blue.s3-website-us-east-1.amazonaws.com`
- **Green** — `deploy-lab-677746514416-green.s3-website-us-east-1.amazonaws.com`

Only one is "active" at a time, tracked by `deployment.json`.

## Workflows

### Deploy & Switch (`.github/workflows/deploy.yml`)
1. Reads `deployment.json` to find the inactive environment
2. Deploys new content to the inactive bucket
3. Health-checks the deployment (HTTP 200)
4. Switches `active_environment` in `deployment.json`
5. Commits the updated state

### Rollback (`.github/workflows/rollback.yml`)
1. Reads `deployment.json` to find the currently active environment
2. Switches back to the previous environment (no redeployment needed)
3. Records rollback reason in history
4. Commits the updated state

## Deployment State (`deployment.json`)
Tracks active environment, version, deployer, timestamp, and full history.

## Key Learnings
- Blue/green eliminates downtime during deployments
- The inactive environment is always ready for the next deploy
- Rollback is instant — just switch the pointer
- Deployment history provides an audit trail

## Lab Results

| Step | Status |
|------|--------|
| Terraform apply (8 resources) | Passed |
| Blue site live (v1.0.0) | Passed |
| Green site live (v2.0.0) | Passed |
| Deploy workflow (blue → green) | Passed |
| Rollback workflow (green → blue) | Passed |
