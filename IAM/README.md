# IAM Security Tools

##  IAM Credentials Report

- A report that lists all your account's users and the status of their various credentials

## IAM Access Advisor

- Access advisor shows the service permissions granted to a user and when those services were last accessed.
- You can use this information to revise your policies.

## IAM Access Analyzer

Find out which resources are shared externally
• S3 Buckets
• IAM Roles
• KMS Keys
• Lambda Functions and Layers
• SQS queues
• Secrets Manager Secrets
• Define Zone of Trust = AWS Account or AWS Organization
• Access outside zone of trusts => findings

![Zone of Trust](./IAM_access_analyzer.png)

###  IAM Access Analyzer Policy Validation
- Validates your policy against IAM policy grammar and best practices
- General warnings, security warnings, errors, suggestions
- Provides actionable recommendations

###  IAM Access Analyzer Policy Generation
- Generates IAM policy based on access activity
- CloudTrail logs is reviewed to generate the policy with the fine-grained permissions and the appropriate Actions and Services
- Reviews CloudTrail logs for up to 90 days

![Policy Generator](./IAM_Policy_generator.png)

