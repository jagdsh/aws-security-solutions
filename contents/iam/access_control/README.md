# Access Control

## ABAC – Attribute-Based Access Control

- Defines fine-grained permissions based on user attributes
- Example: department, job role, team name,
- Instead of creating IAM roles for every team, use ABAC to group attributes to identify which resources a set of users can access
- How do we do it? === Allow operations when the principal’s tags matches the resource tag
- Helpful in rapidly-growing environments
  - Since you can add the resources and tag them and need not worry about IAM policies

![ABAC](./abac_permissions.png)

## ABAC - Example

![ABAC Policy](./abac_policy.png)

## ABAC vs. RBAC

- Role-Based Access Control (RBAC)
  - Defines fine-grained permissions based on user role or job function
  - Example: Administrator, DB Admins, DevOps, …
  - Create different policies for different job functions
  - Disadvantage: must update policies when resources are added
- Attribute-Based Access Control (ABAC) – Advantages
  - Scale permissions easily (no need to update policies when new resources added)
  - Permissions automatically granted based on attributes
  - Require fewer policies (you don’t create different policies for different job functions)
  - Ability to use users' attributes from corporate directory (e.g., SAML 2.0-based IdP or Web IdP)

## Multi Factor Authentication - MFA

- Users have access to your account and can possibly change configurations or delete resources in your AWS account
- You want to protect your Root Accounts and IAM users
- MFA = password you know + security device you own
- Main benefit of MFA:
  if a password is stolen or hacked, the account is not compromised
![MFA](./mfa.png)

### MFA devices options in AWS

![Virtual MFA device, Universal 2nd Factor (U2F) Security Key](./mfa_devices.png)

![Hardware Key Fob MFA Device, AWS GovCloud (US)](./mfa_devices_1.png)

### Amazon S3 – MFA Delete

- MFA (Multi-Factor Authentication) – force users to generate a code on a device (usually a mobile phone or hardware) before doing important operations on S3
- MFA will be required to:
  - Permanently delete an object version - For destruction activity
  - Suspend Versioning on the bucket
- MFA won’t be required to:
  - Enable Versioning
  - List deleted versions
- To use MFA Delete, Versioning must be enabled on the bucket
- Only the bucket owner (root account) can enable/disable MFA Delete

### IAM Conditions – MultiFactorAuthPresent

- Restrict access to AWS services for users not authenticated using MFA
- aws:MultiFactorAuthPresent
- Compatible with the AWS Console and the AWS CL

![MultiFactorAuthPresent](./mfa_iam_conditions.png)

![MultiFactorAuthPresent](./mfa_iam_conditions_numeric_less.png)

Grants access only if the user authenticated with MFA within the last 300 seconds

### Not Authorized to Perform iam:DeleteVirtualMFADevice **[Troubleshooting]**

- This error happens even the user has the correct IAM permissions
- This happens if someone began assigning a virtual MFA device to a user and then cancelled the process
  - E.g. created an MFA device for the user but never activates it
  - Must delete the existing MFA device then associate a new device
  - AWS recommends a policy that allows a user to delete their own virtual MFA device only if they are authenticated using MFA
- To fix the issue, the administrator must use the AWS CLI or AWS API to remove the existing but deactivated device

## IAM Credentials Report

- IAM users and the status of their passwords, access keys, and MFA devices
- Download using IAM Console, AWS API, AWS CLI, or AWS SDK
- Helps in auditing and compliance
- Generated as often as once every 4 hours by using API call

![Report](./iam_credentials_report.png)

### Managing Aged Access Keys through AWS Config Remediations

![Aged Access Keys with Config](./managing_aws_config.png)

- This is the better way to automate the key rotation than IAM Credentials report. **[Troubleshooting]**

## IAM Roles for Services

- Some AWS service will need to perform actions on your behalf
- To do so, we will assign permissions to AWS services with IAM Roles
- Common roles:
  - EC2 Instance Roles
  - Lambda Function Roles
  - Roles for CloudFormation

![IAM Roles](./iam_roles.png)

### Delegate Passing Permissions to AWS Services

- You can grant users permissions to pass an IAM role to an AWS service
- Ensure that only approved users can configure an AWS service with an IAM role that grants permissions
- Grant iam:PassRole permission to the user’s IAM user, role, or group
- PassRole is not an API call (no CloudTrail logs generated)
  - Review CloudTrail log that that created or modified the resource receiving the IAM role

![Delegate Permission](./iam_delegate_permissions.png)

Allow User to Pass Only Approved Roles
