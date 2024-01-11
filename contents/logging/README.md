## Logging

To help compliance requirements, AWS provides many service-specific security and audit logs

- Service Logs include:
    - CloudTrail trails - trace all API calls
    - Config Rules - for config & compliance over time
    - CloudWatch Logs - for full data retention
    - VPC Flow Logs - IP traffic within your VPC
    - ELB Access Logs - metadata of requests made to your load balancers
    - CloudFront Logs - web distribution access logs
    - WAF Logs - full logging of all requests analyzed by the service
- Logs can be analyzed using AWS Athena if they’re stored in S3 *Important exam question*
- You should encrypt logs in S3, control access using IAM & Bucket Policies, MFA
- Move Logs to Glacier for cost savings


Whitepaper [Archieved]: https://d0.awsstatic.com/whitepapers/compliance/AWS_Security_at_Scale_Logging_in_AWS_Whitepaper.pdf
Lates in: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/detection.html