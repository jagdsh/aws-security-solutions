# Amazon ECR

- ECR = Elastic Container Registry
- Store and manage Docker images on AWS
- Private and Public repository (Amazon ECR Public Gallery https://gallery.ecr.aws)
- Fully integrated with ECS, backed by Amazon S3
- Access is controlled through IAM (permission errors => policy)
- Supports image vulnerability scanning, versioning, image tags, image lifecycle

![ECR and ECS integration](./ecr_ecs.png)

## Elastic Container Registry (ECR) with KMS

- ECR Repositories can be encrypted at rest using your KMS Keys
- Container Images are encrypted using a unique Data Encryption Key (Envelope Encryption)
- Can enable encryption only at Repository Creation
- ECR creates KMS Grant on your behalf to interact with KMS
- KMS Grant has kms:DescribeKey, kms:Decrypt, kms:GenerateDataKey, kms:RetireGrant (when you delete the repository)

![ECR with KMS](./ecr_kms.png)

## ECR – Image Scanning

- Find software vulnerabilities in your container images
- Basic Scanning
- ECR uses an open-source Common Vulnerabilities and Exposures (CVEs) database
- Configure repository to scan on push or manually
- Advanced Scanning
- ECR uses Amazon Inspector
- Automated and continuous scanning of images
- Scan both OS and programming language packages vulnerabilities
- ECR Scan Filters: specify which repositories are scanned
- Example: _prod_ to match anything with prod in the repo name

![ECR Image Scanning](./ecr_image_scanning.png)

## ECR – Cross-Account Access

![ECR Cross Account](./ecr_cross_account.png)

````json
{
    "Version": "2008-10-17",
  "Statement": [
      {
          "Effect": "Allow",
      "Principal": {
          ```
        "AWS": "arn:aws: iam:: 444455556666:root"
    ```
      },
      "Action": [
          "ecr:GetDownloadUrlForLayer",
        "ecr: BatchGet Image",
        "ecr:BatchCheckLayerAvailability",
        "ecr: Put Image",
        "ecr:InitiateLayerUpload",
        "ecr:UploadLayerPart",
        "ecr:CompleteLayerUpload"
      ]
    }
  ]
}
````

## ECR – **[Troubleshooting]**

- HTTP 403 (Forbidden) or “No Basic Auth Credentials” Error
- This error might happen from the docker push and docker pull commands even if you have successfully authenticated to Docker using aws ecr get-login-password
- Reasons:
  - You have authenticated to a different AWS region
  - You have authenticated to an ECR repository you don’t have permissions for
  - Authenticated Token has expired (valid for 12 hours)

## ECS Integration with SSM Parameter Store & Secrets Manager

- ECS can inject sensitive data into your containers as environment variables
- Use SSM Parameter Store or Secrets Manager to store sensitive data
- Reference sensitive data in the container definition

![ECS Integration Secrets Manager](./ecs_integration_ssm_secrets_manager.png)

## Amazon EKS – Logging

- Pod events, node events,
- Amazon EKS default event TTL is 60 minutes (can’t be changed)
- To keep history of Kubernetes events beyond 60 minutes, send them to CloudWatch Logs
- Amazon EKS Control Plane Logging
- Send EKS Control Plane logs to CloudWatch Logs
- Audit logs, diagnostic logs, controller logs, …
- Ability to select the exact log types to send to CW Logs

![EKS Logging](./eks_logging.png)
