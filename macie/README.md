# Macie

![Macie](./macie.png)

- Amazon Macie is a fully managed data security and data privacy service that uses machine learning and pattern matching to discover and protect your sensitive data in AWS.
- Macie helps identify and alert you to sensitive data, such as personally identifiable information (PII).
- Used to analyze and identify sensitive data in your S3 buckets
- *Managed Data Identifier:*
  • A set of built-in criteria that are designed to detect specific type of sensitive data
  • Examples: credit cards numbers, AWS Credentials, bank accounts
- *Custom Data Identifier:*
  - A set of criteria that you define to detect sensitive data
  - Regular expression, keywords, proximity rule
  - Examples: employee IDs, customer account numbers
- You can use *Allow Lists* to define a text pattern to ignore (e.g., public phone numbers)

![Custom Identifier](./custom_identifier.png)

## Findings

- A report of a potential issue or sensitive data that Macie found
- Each finding has a severity rating, affected resource, datetime,
- Sensitive Data Discovery Result
  - A record that logs details about the analysis of an S3 object
  - Configure Macie to store the results in S3, then query using Athena
- Suppression Rules – set of attribute-based filter criteria to archive findings automatically
- Findings are stored for 90 days
- Review findings using AWS Console, EventBridge, Security Hub

## Findings Types

- *Policy Findings*
  - A detailed report of policy violation or issue with the security of S3 bucket
  - Examples: default encryption is disabled, bucket is public, …
  - ${\color{yellow}Policy:IAMUser/S3BucketEncryptionDisabled, Policy:IAMUser/S3BucketPublic}$
  - Detect changes only after you enable Macie
- *Sensitive Data Findings*
  - A detailed report of sensitive data that’s found in S3 buckets
  - Examples: Credentials (private keys), Financial (credit card numbers), …
  - ${\color{yellow}SensitiveData:S3Object/Credentials, SensitiveData:S3Object/Financial}$
  - For Custom Data Identifier SensitiveData:S3Object/CustomIdentifier

## Multi-Account Strategy

[Multi Account Strategy](./macie_multi_account_strategy.png)

- You can manage multiple accounts in Macie
- Associate the Member accounts with the Administrator account
  - Through an AWS Organization
  - Sending invitation through Macie
  - Supports Delegated Administrator in an AWS Organization
- Administrator account can:
  - Add and remove member accounts
  - Have access to all S3 sensitive data and settings for all accounts
  - Manage Automated Sensitive Data Discovery and run Data Discovery jobs
  - Manage Data Identifiers and Findings
