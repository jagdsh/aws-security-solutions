# Signer & Verified Acess

## AWS Signer

- Fully managed Code-Signing service to ensure trust & integrity of your code
- Manages Code-Signing certificate public and private keys
- Code is validated against a digital signature to confirm that the code is unaltered and from a trusted publisher
- Code-Signing for AWS Lambda
- Digitally sign code packages for Lambda
- Enforce only trusted code runs in Lambda functions
- Not supported for Container Lambda functions
- Code-Signing for AWS IoT
- Sign code you create for AWS IoT and Amazon FreeRTOS
- Integrated with ACM to generate/import certificates

![Signer](./signer.png)

### Revoking Signing Profile

- For Lambda Functions, you can revoke the signature of Lambda deployment package (invalidate it)
- Causing it to fail Lambda signature checks
- Either revoke individual signatures using RevokeSignature API call or revoke a Signing Profile using RevokeSigningProfile API call
- Note: Use only in critical scenarios (irreversible)

![Signer Revoking Policy](./signer_revoking_policy.png)

## AWS Verified Access

- Provide secure access to corporate applications without a VPN
- Remotely connect to your applications from any network
- Implement Zero Trust principles
- Go beyond network security: user identity, device security state…
- Fine-grained access for your applications
- Validates each application access request in real-time
- Authenticate users against IAM Identity Center or 3rd party OIDC IdP (e.g., Okta, CrownStrike…)
- Integrates with AWS WAF to prevent threats (e.g., SQL Injection, XSS…)
- Logs to Amazon S3, CloudWatch Logs, Kinesis Data Firehose, or 3rd party

![Verified Access](./verified_access.png)


