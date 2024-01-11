# AWS Secrets Manager

- Newer service, meant for storing secrets
- Capability to force rotation of secrets every X days
- Automate generation of secrets on rotation (uses Lambda)
- Integration with Amazon RDS (MySQL, PostgreSQL, Aurora)
- Secrets are encrypted using KMS
- Mostly meant for RDS integration

## AWS Secrets Manager – Multi-Region Secrets

- Replicate Secrets across multiple AWS Regions
- Secrets Manager keeps read replicas in sync with the primary Secret
- Ability to promote a read replica Secret to a standalone Secret
- Use cases: multi-region apps, disaster recovery strategies, multi-region DB

![Multi Region Secrets Manager](./multi_region_secrets.png)

## KMS with Secrets Manager

- Secrets Manager uses KMS to encrypt/decrypt every version of every Secret value
- Each Secret value is encrypted with a unique data key (Envelope Encryption)
- Specify the KMS key or use AWS Managed Key (aws/secretsmanager)
- Works only with Symmetric KMS Keys
- Encryption process takes place in Secrets Manager
- Note: you must have access to both the KMS key and the Secret in Secrets Manager

![KMS Secrets Manager](./kms_secrets_manager.png)

## Secrets Manager – Secrets Rotation

- Automatically and periodically updating a Secret
- Automated password rotation for integrated databases
- RDS, Redshift, DocumentDB, other databases…
- Credentials are changed in the Secret and the database
- Secrets Manager uses Lambda function to rotate Secrets
- When you Enable Secret Rotation, the Secret is rotated immediately
- Note: Lambda function must have access to both Secrets Manager and your database (if Lambda in VPC private subnet, use VPC Endpoints or NAT Gateway)

![Secrets Rotation](./secrets_rotation.png)

### Secrets Manager with Lambda integration

![Secrets Manager Lambda integration](./secrets_lambda_integration.png)

### Secrets Manager with ECS integration

![Secrets Manager ECS integration](./secrets_manager_ecs_integration.png)

## Secrets Manager – Resource Policy

- Specify who can access a Secret and what actions an IAM identity can perform
- Use cases:
  - Grant access to a single Secret for multiple users
  - Enforcing permissions (e.g., adding an explicit deny to a Secret)
  - Sharing a Secret between AWS accounts

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "secretsmanager:*",
      "Principal": {
        "AWS": "arn:aws: iam:: 123456789012:user/Mary"
      },
      "Resource": "*"
    }
  ]
}
```
