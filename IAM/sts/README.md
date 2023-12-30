# Amazon STS - Seurity Token Service

- Allows to grant limited and temporary access to AWS resources.
- Token is valid for up to one hour (must be refreshed)
- AssumeRole
  - Within your own account: for enhanced security
  - Cross Account Access: assume role in target account to perform actions there
- AssumeRoleWithSAML
  - return credentials for users logged with SAML
- AssumeRoleWithWebIdentity
  - return creds for users logged with an IdP (Facebook Login, Google Login, OIDC compatible…)
  - AWS recommends against using this, and using Cognito instead
- GetSessionToken
  - for MFA, from a user or AWS account root user

## Using STS to Assume a Role

- Define an IAM Role within your account or cross-account
- Define which principals can access this IAM Role
- Use AWS STS (Security Token Service) to retrieve credentials and impersonate the IAM Role you have access to (AssumeRole API)
- Temporary credentials can be valid between 15 minutes to 1 hour

![STS Assume Role](./sts_assume_role.png)

### Cross-Account Access with STS

![Cross-Account with STS](./cross-account-with-sts.png)

## STS – Version 1 vs.Version 2

- STS Version 1
  - By default, STS is available as a global single endpoint https://sts.amazonaws.com
  - Only support AWS Regions that are enabled by default
  - Option to enable “All Regions”
- STS Version 2
  - Version 1 tokens do not work for new AWS Regions (e.g., me-south-1)
  - Regional STS endpoints is available in all AWS Regions
  - Reduce latency, built-in redundancy, increase session token validity
  - STS Session Tokens from regional endpoints (STS Version 2) are valid in all AWS Regions

![Cross-Account with STS](./sts_v1_v2.png)

- Error : "An error occurred (AuthFailure) when calling the DescribeInstances operation: AWS was not able to validate the provided access credentials.”
- To solve, two options:
  1. Use the Regional STS Endpoint (any region) which will return STS Tokens Version 2. Use the closest regional endpoint for lowest latency
  2. By default, the AWS STS calls to the STS global endpoint issues session tokens which are of Version 1 (Default AWS Regions). You can configure STS global endpoint to issue STS Tokens Version 2 (All AWS Regions)

## Granting Access to 3rd Party using External ID

- External ID is a piece of data that can be passed to AssumeRole API
- Allowing the IAM role to be assumed only if a certain value is present (External ID)
- It solves the Confused Deputy problem
- Prevent any other customer from tricking 3rd party into unwittingly accessing your resources

![Granting Access to 3rd Party using External ID](./granting_3rd_party_ext_id_policy.png)

![Granting Access to 3rd Party using External ID](./granting_3rd_party_ext_id.png)

## Revoking IAM Role Temporary Credentials

- Users usually have a long session duration time (e.g., 12 hours)
- If credentials are exposed, they can be used for the duration of the session
- Immediately revoke all permissions to the IAM role’s credentials issued before a certain time
- AWS attaches a new inline IAM policy to the IAM role that denies all permissions (forces users to reauthenticate) if the token is too old
- Doesn’t affect users who assumes the IAM role after you revoke sessions (don’t worry about deleting the policy)

![AWSRevokeOlderSessions Policy](./aws-revoke-older-sessions.png)
AWSRevokeOlderSessions Policy

![IAM Role Temporary Credentials](./revoke-iam-temporary-credentials.png)