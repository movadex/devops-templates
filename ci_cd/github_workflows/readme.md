# GitHub Actions Workflows for AWS Deployments

This folder contains GitHub Actions workflow templates for deploying applications to AWS Elastic Beanstalk (EB) and Lambda.

## Prerequisites

*   AWS Account & appropriate IAM permissions.
*   GitHub Repository Secrets: `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`.

## Workflows Overview

Place these files in your project's `.github/workflows/` directory.

1.  **`eb_docker.yml`**: Deploys Dockerized apps to Elastic Beanstalk.
    *   **Flow:** Builds Docker image -> Pushes to ECR -> Creates EB version using S3 artifacts -> Updates EB environment.
    *   **Requires:** ECR registry, S3 bucket for artifacts, EB app/env names, EB config folder path (`EB_CONFIGS_FOLDER` containing `eb-template.json`), Docker image name.
    *   **Note:** `eb-template.json` should reference `$GIT_SHA` for the image tag; the workflow substitutes this and creates `Dockerrun.aws.json`.

2.  **`lambda_docker.yml`**: Deploys Dockerized apps to Lambda.
    *   **Flow:** Builds Docker image -> Pushes to ECR -> Updates Lambda function code with the new image URI.
    *   **Requires:** ECR registry, Docker image name, Lambda function name.

3.  **`lambda.yml`**: Deploys non-Dockerized apps to Lambda.
    *   **Flow:** Zips project code -> Updates Lambda function code with the zip file.
    *   **Requires:** Lambda function name.

## Configuration

*   **Secrets:** Add `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` to your GitHub repository secrets.
*   **Environment Variables:** Edit the `env:` block in the chosen workflow file to match your AWS resource names (Region, ECR URL, S3 Bucket, EB/Lambda names, etc.).
*   **Triggers:** Adjust the `on:` section (e.g., `push`, `workflow_dispatch`) to control when the workflow runs.

## Usage Example (Using `lambda_docker.yml`)

1.  Copy `lambda_docker.yml` to `.github/workflows/deploy-lambda.yml`.
2.  Edit the `env:` block in `deploy-lambda.yml`:
    ```yaml
    env:
      AWS_REGION: us-east-1
      DOCKER_IMAGE_NAME: my-lambda-app
      ECR_REGISTRY: 123456789012.dkr.ecr.us-east-1.amazonaws.com
      LAMBDA_FUNCTION_NAME: myLambdaFunctionProd
    ```
3.  Configure AWS secrets in GitHub.
4.  Commit and push. The workflow will run based on the trigger defined in the `on:` section.
