# AWS Security solutions

This is a personal notes capturing various various AWS resources which are helpful in securing application. This repo containsa an list of AWS resources which can be used for detecting, isolating and analyzing.

## Index

### Threat Detection and Incident Response

- [Guard Duty](./guard_duty/README.md)
- [Security Hub](./security-hub/README.md)
- [Amazon Detective](./detective/README.md)
- [Security Testing](./security-testing/README.md)
  - [Penetration Testing](./security-testing/README.md#penetration-testing)
- [Compromised Resources](./compromised-resources/README.md)
  - [EC2](./compromised-resources/README.md#compromised-ec2-instance)
  - [S3](./compromised-resources/README.md#compromised-s3-bucket)
  - [ECS](./compromised-resources/README.md#compromised-ecs-cluster)
  - [Standalone Container](./compromised-resources/README.md#compromised-standalone-container)
  - [RDS](./compromised-resources/README.md#compromised-rds-instance)
  - [AWS Credentials](./compromised-resources/README.md#compromised-aws-credentials)
  - [IAM Role](./compromised-resources/README.md#compromised-iam-role)
  - [AWS Account](./compromised-resources/README.md#compromised-aws-account)
- [EC2 Troubleshooting](./EC2/README.md)
  - [Remediating EC2 Exposed Key Pair](./EC2/README.md#remediating-ec2-exposed-key-pair)
  - [EC2 SSH Connection workflow](./EC2/README.md#ec2-ssh-connection-workflow)
  - [EC2 Serial Console](./EC2/README.md#ec2-serial-console)
  - [Lost SSH EC2 Key Pair - Linux](./EC2/README.md#lost-ssh-ec2-key-pair-linux)
  - [EC2 Rescue tool For Linux](./EC2/README.md#for-linux)
  - [EC2 Rescue tool For Windows](./EC2/README.md#for-windows)
- [IAM Policy](./IAM/README.md)
  - [Access Analyzer](./IAM/README.md#iam-access-analyzer)
  - [Policy Generator](./IAM/README.md#iam-access-analyzer-policy-generation)
- [Policy](./policy/README.md)
  - [Acceptable Use Policy](./policy/README.md#acceptable-use-policy)
  - [AWS Abuse Report](./policy/README.md#aws-abuse-report)

### Security Logging and Monitoring

- [Amazon Inspector](./inspectod/README.md)
- [CloudWatch](./cloudwatch/README.md)
- [Athena](./athena/README.md)
- [CloudTrail](./cloudtrail/README.md)
  - [Summary](./cloudtrail/README.md#summary-monitor-account-activity)
- [Macie](./macie/README.md)
- [S3 Event Notification](./s3/event-notifications/README.md)
- [VPC Flow Logs](./vpc/flow-logs/README.md)
- [VPC Traffic Mirroring](./vpc/traffic-mirroring/README.md)
- [Route 53 Query Logging](./route53/README.md)
- [OpenSearch](./opensearch/README.md)

### Infrastructure and Security

- [Bastion Host](./infrastructure-and-security/README.md#bastion-host)
- [Site to Site VPN](./infrastructure-and-security/README.md#site-to-site-vpn)
- [Client VPN](./infrastructure-and-security/README.md#client-vpn)
- [VPC Peering](./infrastructure-and-security/README.md#vpc-peering)
- [VPC Endpoints](./vpc/endpoints/README.md)
- [Private Link](./vpc/private-link/README.md)
- [Network Access Control List](./security_groups_NACLs/README.md#network-access-control-list)
- [Security Groups](./security_groups_NACLs/README.md#security-group-vs.-nacls)
- [Transit Gateway](./transit-gateway/README.md)
- [CloudFront](./cloudfront/README.md)
- [WAF - Web application Firewall](./firewall-and-shield/README.md#aws-waf–web-application-firewall)
- [Shield](./firewall-and-shield/README.md#aws-shield)
- [DDoS Resiliency](./firewall-and-shield/README.md#aws-best-practices-for-ddos-resiliency)
- [API Gateway](./api-gateway/README.md#api-gateway)
- [AWS Artifact](./api-gateway/README.md#aws-artifacts-(not-really-a-service))
- [DNSSEC](./route53/README.md#dns-security-extensions-(dnssec))
- [Network Firewall](./firewall-and-shield/README.md#aws-network-firewall)
- [Simple Email Service](./simple-email-service/README.md)

### Identity and Access management

- [IAM](./iam/README.md)
  - [IAM Policies](./iam/README.md#)
  - [IAM Condition](./iam/README.md#)
  - [IAM Permission](./iam/README.md#)
  - [Identity Based vs Resource-based Policy](./iam/README.md#)
    - [Amazon EventBridge – Security](./event-bridge/README.md#amazon-eventbridge–security)
    - [Simple Queue Service (SQS)](./sqs/README.md#sqs-queue-access-policy)
  - [Attribute based Access Control (ABAC)](./iam/access_control/README.md#abac–attribute-based-access-control)
    - [ABAC vs RBAC](./iam/access_control/README.md#abac-vs.-rbac)
    - [IAM Multi-Factor Authentication](./iam/access_control/README.md#multi-factor-authentication-mfa)
  - [IAM Credential Report](./iam/access_control/README.md#iam-credentials-report)
  - [IAM Roles for Services](./iam/access_control/README.md#iam-roles-for-services)
  - [Amazon STS - Seurity Token Service](./iam/sts/README.md#amazon-sts-seurity-token-service)
  - [Sample SCP](./iam/sts/README.md#sample-scp)
  - [Instance Metadata Service (IMDS)](./iam/instance_metadata_service_imds/README.md#aws-ec2-instance-metadata-service-(imds))
  - [IAM in S3](./iam/s3/README.md#iam-in-s3)
    - [How Authorization Works in Amazon S3](./iam/s3/README.md#how-authorization-works-in-amazon-s3)
    - [Object Operation Request example: 3 accounts](./iam/s3/README.md#object-operation-request-example:-3-accounts)
    - [S3 Canned ACL – Deep Dive](./iam/s3/README.md#s3-canned-acl–deep-dive)
    - [Bucket Policy – Restrict Public IP Address](./iam/s3/README.md#s3-bucket-policy–restrict-public-ip-address)
    - [Bucket Policy – Restrict Public IP Address](./iam/s3/README.md#s3-bucket-policy–restrict-public-ip-address)
    - [VPC Interface Endpoint for S3](./iam/s3/README.md#vpc-interface-endpoint-for-s3)
    - [Regain Access to Locked S3 Buckets](./iam/s3/README.md#regain-access-to-locked-s3-buckets)
    - [Access Points](./iam/s3/README.md#s3–access-points)
    - [CORS in S3](./iam/s3/README.md#what-is-cors)
  - [Cognito](./iam/cognito/README.md)
    - [Cognito User Pools](./iam/cognito/README.md#cognito-user-pools-(cup)–user-features)
    - [Cognito Identity Pools – IAM Roles](./iam/cognito/README.md#cognito-identity-pools–iam-roles)
    - [Cognito User Pools vs Identity Pools](./iam/cognito/README.md#cognito-user-pools-vs-identity-pools)
    - [Cognito User Pool Groups](./iam/cognito/README.md#cognito-user-pool-groups)
  - [Identity Federation](./iam/identity/README.md#identity-federation-in-aws)
  - [IAM Identity Center](./iam/identity/README.md#aws-iam-identity-center)
  - [Active Directory](./iam/active_directory/README.md#active-directory)

### Data Protection

- [Encryption](./data_protection/README.md#why-encryption?)
- [CloudHSM](./cloudhsm/README.md#cloudhsm)
- [AWS KMS](./kms/README.md#aws-kms-(key-management-service))
  - [Key Material Origin](./kms/README.md#kms-key-material-origin)
  - [Multi-Region Keys](./kms/README.md#kms-multi-region-keys)
  - [Envelope Encryption](./kms/README.md#envelope-encryption)
  - [KMS Symmetric – API Summary](./kms/README.md#kms-symmetric–api-summary)
  - [Encryption SDK](./kms/README.md#encryption-sdk)
  - [Key Rotation](./kms/README.md#key-rotation)
  - [Asymmetric Encryption](./kms/README.md#asymmetric-encryption)
  - [KMS with SSM Parameter Store](./kms/README.md#kms-with-ssm-parameter-store)
- [Secrest Manager](./secrets_manager/README.md)
  - [Secrets Manager – Secrets Rotation](./secrets_manager/README.md#secrets-manager–secrets-rotation)
  - [KMS with Secrets Manager](./secrets_manager/README.md#kms-with-secrets-manager)
  - [Secrets Manager – Resource Policy](./secrets_manager/README.md#secrets-manager–resource-policy)
- [S3 Encryption](./s3/encryption/README.md#object-encryption)
  - [S3 Encryption Summary](./s3/encryption/README.md#s3-encryption-for-objects-summary)
  - [Default Encryption vs Bucket Policies](./s3/encryption/README.md#amazon-s3–default-encryption-vs.-bucket-policies)
  - [S3 Bucket Key for SSE-KMS Encryption](./s3/encryption/README.md#s3-bucket-key-for-sse-kms-encryption)
  - [S3 Batch – Object Encryption](./s3/encryption/README.md#s3-batch–object-encryption)
- [S3 Glacier Vault Lock](./s3/README.md#s3-glacier-vault-lock)
  - [Vault Lock Process](./s3/README.md#vault-lock-process)
- [Moving between Storage Classes](./s3/README.md#moving-between-storage-classes)
- [S3 Lifecycle Rules](./s3/README.md#lifecycle-rules)
- [Replication (CRR & SRR)](./s3/README.md#replication-(crr-&-srr))
- [Load Balancing](./load_balancing/README.md#load-balancing)
  - [Network Load Balancer (v2)](./load_balancing/README.md#network-load-balancer-(v2))
  - [Sticky Sessions (Session Affinity)](./load_balancing/README.md#sticky-sessions-(session-affinity))
  - [SSL/TLS - Basics](./load_balancing/README.md#ssl/tls-basics)
  - [Elastic Load Balancer](./load_balancing/README.md#elastic-load-balancer)
    - [### SSL – Server Name Indication (SNI)](./load_balancing/README.md#elastic-load-balancer)
    - [HTTPS/SSL Listener – Security Policy](./load_balancing/README.md#https/ssl-listener–security-policy)
    - [NLB - SSL/TLS & HTTPS](./load_balancing/README.md#nlb-ssl/tls-&-https)
- [AWS Certificate Manager (ACM)](./acm/README.md#aws-certificate-manager-(acm))
  - [ACM – Good to know](./acm/README.md#acm–good-to-know)
  - [AWS Private Certificate Authority (CA)](./acm/README.md#aws-private-certificate-authority-(ca))
  - [Validation Techniques](./acm/README.md#validation-techniques)
  - [Process to Manually Create a Certificate](./acm/README.md#process-to-manually-create-a-certificate)
  - [Monitor Expired Imported Certificates](./acm/README.md#monitor-expired-imported-certificates)
- [AWS Backup](./data_protection/README.md#aws-backup)
  - [Vault Lock](./data_protection/README.md#vault-lock)
- [Amazon Data Lifecycle Manager](./data_protection/README.md#amazon-data-lifecycle-manager)
- [Nitro Enclaves](./data_protection/README.md#nitro-enclaves)

### Management and Security Governance

- [AWS Organizations](./organizations_and_accout_tower/README.md#aws-organizations)
  - [SCP Hierarchy](./organizations_and_accout_tower/README.md#scp-hierarchy)
  - [SCP Examples Blocklist and Allowlist strategies](./organizations_and_accout_tower/README.md#scp-examples-blocklist-and-allowlist-strategies)
  - [Reserved Instances](./organizations_and_accout_tower/README.md#reserved-instances)
  - [IAM Policies](./organizations_and_accout_tower/README.md#iam-policies)
  - [Tag Policies](./organizations_and_accout_tower/README.md#tag-policies)
- [AWS Control Tower](./organizations_and_accout_tower/README.md#aws-control-tower)
  - [Account Factory](./organizations_and_accout_tower/README.md#account-factory)
  - [Detect and Remediate Policy Violations](./organizations_and_accout_tower/README.md#detect-and-remediate-policy-violations)
  - [Guardrails Levels](./organizations_and_accout_tower/README.md#guardrails-levels)
- [AWS Config](./config_cost_explorer/README.md#aws-config)


## Appendix

    *Vulnerability* – weakness in a system, system security procedures, internal controls, or implementation that could be exploited

    *Exploit* – code that takes advantage of software vulnerability or security flaws

    *Payload* – a malware that the attacker intends to deliver to the victim

    *Automated Vulnerability Scanner* – a tool that run automated scans of an IT environments to detect vulnerabilities, example : nessus scan (note: you don't need to know that tool for the exam)

    *Common Vulnerabilities and Exposures (CVE)* – a list of publicly disclosed security flaws.

    *Common Vulnerability Scoring System (CVSS)* – a way to produce a numerical score reflecting the severity of a vulnerability or a security flaws

    *Availability Zones* - Each Region has multiple, isolated locations known as Availability Zones. The code for Availability Zone is its Region code followed by a letter identifier. For example, us-east-1a.

    **[Troubleshooting]** - Information which might be useful when we run into errors and issues.

    **[CMK]** - The CMK contains the key material used to encrypt and decrypt data.
