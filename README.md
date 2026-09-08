# GitHub Actions Workflows

Reusable GitHub Actions workflows for deploying common project types. Call them from any repo via `workflow_call` instead of copy-pasting YAML into each project.

## Workflows

| Workflow                                                                                | Purpose                                                                                                  |
| ---------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| [deploy-angular-s3.yml](.github/workflows/deploy-angular-s3.yml)                         | Build an Angular app and deploy it to an S3 bucket, optionally invalidating a CloudFront distribution.   |
| [deploy-django-ec2-docker.yml](.github/workflows/deploy-django-ec2-docker.yml)           | Build a Docker image and deploy it to an EC2 instance over SSH using Docker Compose.                     |

## Usage

Reference a workflow from a consumer repo's own `.github/workflows/*.yml`:

### Angular → S3 + CloudFront

```yaml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    uses: mohasinmudassar/Github-Actions-Yaml/.github/workflows/deploy-angular-s3.yml@main
    with:
      s3-bucket: my-app-bucket
      aws-region: us-east-1
      cloudfront-distribution-id: EXXXXXXXXXXXXX
    secrets:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```

### Django (or any Dockerized app) → EC2

```yaml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    uses: mohasinmudassar/Github-Actions-Yaml/.github/workflows/deploy-django-ec2-docker.yml@main
    with:
      dockerfile-path: deploy/live/django/Dockerfile
      image-name: my-django-app:latest
      ec2-host: ec2-xx-xx-xx-xx.compute.amazonaws.com
      remote-path: /home/ubuntu/my-django-app
    secrets:
      EC2_SSH_KEY: ${{ secrets.EC2_SSH_KEY }}
```

Each workflow's full set of inputs (with defaults) and required secrets is documented inline in its YAML file.

## Requirements

- The consumer repo must set the secrets listed above in its own repository settings.
- EC2 targets need Docker and Docker Compose already installed, with a `docker-compose.yml` present at `remote-path`.
