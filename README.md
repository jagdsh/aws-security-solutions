# AWS Security solutions

This is a personal notes capturing various various AWS resources which are helpful in securing application. This repo containsa an list of AWS resources which can be used for detecting, isolating and analyzing.

## Index

### Threat Detection and Incident Response

- [Guard Duty](./contents/guard_duty/README.md)
  - [Few points to remember](./contents/guard_duty/README.md#few-points-to-remember)
  - [Multi-Account Strategy](./contents/guard_duty/README.md#multi-account-strategy)
  - [Findings](./contents/guard_duty/README.md#findings)
  - [GuardDuty Architectures](./contents/guard_duty/README.md#guardduty-architectures)
  - [Trusted and Threat IPs](./contents/guard_duty/README.md#trusted-and-threat-ips)
  - [Supression Rules](./contents/guard_duty/README.md#supression-rules)
  - [Troubleshooting](./contents/guard_duty/README.md#guardduty-didn’t-generate-any-finding-types)
- [Security Hub](./contents/security-hub/README.md)
- [Amazon Detective](./contents/detective/README.md)
- [Security Testing](./contents/security-testing/README.md)
  - [Penetration Testing](./contents/security-testing/README.md#penetration-testing)
- [Compromised Resources](./contents/compromised-resources/README.md)
  - [EC2](./contents/compromised-resources/README.md#compromised-ec2-instance)
  - [S3](./contents/compromised-resources/README.md#compromised-s3-bucket)
  - [ECS](./contents/compromised-resources/README.md#compromised-ecs-cluster)
  - [Standalone Container](./contents/compromised-resources/README.md#compromised-standalone-container)
  - [RDS](./contents/compromised-resources/README.md#compromised-rds-instance)
  - [AWS Credentials](./contents/compromised-resources/README.md#compromised-aws-credentials)
  - [IAM Role](./contents/compromised-resources/README.md#compromised-iam-role)
  - [AWS Account](./contents/compromised-resources/README.md#compromised-aws-account)
- [EC2 Troubleshooting](./contents/EC2/README.md)
  - [Remediating EC2 Exposed Key Pair](./contents/EC2/README.md#remediating-ec2-exposed-key-pair)
  - [EC2 SSH Connection workflow](./contents/EC2/README.md#ec2-ssh-connection-workflow)
  - [EC2 Serial Console](./contents/EC2/README.md#ec2-serial-console)
  - [Lost SSH EC2 Key Pair - Linux](./contents/EC2/README.md#lost-ssh-ec2-key-pair-linux)
  - [EC2 Rescue tool For Linux](./contents/EC2/README.md#for-linux)
  - [EC2 Rescue tool For Windows](./contents/EC2/README.md#for-windows)
- [IAM Policy](./contents/IAM/README.md)
  - [Access Analyzer](./contents/IAM/README.md#iam-access-analyzer)
  - [Policy Generator](./contents/IAM/README.md#iam-access-analyzer-policy-generation)
- [Policy](./contents/policy/README.md)
  - [Acceptable Use Policy](./contents/policy/README.md#acceptable-use-policy)
  - [AWS Abuse Report](./contents/policy/README.md#aws-abuse-report)

### Security Logging and Monitoring

- [Amazon Inspector](./contents/inspector/README.md)
- [CloudWatch](./contents/cloudwatch/README.md)
- [Athena](./contents/athena/README.md)
- [CloudTrail](./contents/cloudtrail/README.md)
  - [Summary](./contents/cloudtrail/README.md#summary-monitor-account-activity)
- [Macie](./contents/macie/README.md)
- [S3 Event Notification](./contents/s3/event-notifications/README.md)
- [VPC Flow Logs](./contents/vpc/flow-logs/README.md)
- [VPC Traffic Mirroring](./contents/vpc/traffic-mirroring/README.md)
- [Route 53 Query Logging](./contents/route53/README.md)
- [OpenSearch](./contents/opensearch/README.md)

### Infrastructure and Security

- [Bastion Host](./contents/infrastructure-and-security/README.md#bastion-host)
- [Site to Site VPN](./contents/infrastructure-and-security/README.md#site-to-site-vpn)
- [Client VPN](./contents/infrastructure-and-security/README.md#client-vpn)
- [VPC Peering](./contents/infrastructure-and-security/README.md#vpc-peering)
- [VPC Endpoints](./contents/vpc/endpoints/README.md)
  - [Diff VPC Endpoints](./contents/vpc/endpoints/README.md#interface-vs-gateway-endpoint)
- [Private Link](./contents/vpc/private-link/README.md)
- [Network Access Control List](./contents/security_groups_NACLs/README.md#network-access-control-list)
- [Security Groups](./contents/security_groups_NACLs/README.md#security-group-vs.-nacls)
- [Transit Gateway](./contents/transit-gateway/README.md)
- [CloudFront](./contents/cloudfront/README.md)
- [WAF - Web application Firewall](./contents/firewall-and-shield/README.md#aws-waf–web-application-firewall)
- [Shield](./contents/firewall-and-shield/README.md#aws-shield)
- [DDoS Resiliency](./contents/firewall-and-shield/README.md#aws-best-practices-for-ddos-resiliency)
- [API Gateway](./contents/api-gateway/README.md#api-gateway)
- [AWS Artifact](./contents/api-gateway/README.md#aws-artifacts-(not-really-a-service))
- [DNSSEC](./contents/route53/README.md#dns-security-extensions-(dnssec))
- [Network Firewall](./contents/firewall-and-shield/README.md#aws-network-firewall)
- [Simple Email Service](./contents/simple-email-service/README.md)

### Identity and Access management

[IAM](./contents/iam/README.md)

- [IAM Policies](./contents/iam/README.md#iam-policies)
  - [Types of Policies](./contents/iam/README.md#types-of-policies)
  - [(Action | Not Action) & (Allow | Deny)](./contents/iam/README.md#(action-|-not-action)-&-(allow-|-deny))
- [IAM Condition](./contents/iam/README.md#iam-policies-condition)
- [IAM Permission](./contents/iam/README.md#)
- [IAM Permission Boundaries](./contents/iam/README.md#iam-permission-boundaries)
- [Evaluation Logic](./contents/iam/README.md#evaluation-logic)
- [IAM Roles vs Resource Based Policies](./contents/iam/README.md#iam-roles-vs-resource-based-policies)
- [Amazon EventBridge – Security](./contents/iam/README.md#amazon-eventbridge)
- [Simple Queue Service (SQS)](./contents/iam/README.md#simple-queue-service-(sqs))
- [Attribute based Access Control (ABAC)](./contents/iam/access_control/README.md#abac–attribute-based-access-control)
  - [ABAC vs RBAC](./contents/iam/access_control/README.md#abac-vs.-rbac)
  - [IAM Multi-Factor Authentication](./contents/iam/access_control/README.md#multi-factor-authentication-mfa)
- [IAM Credential Report](./contents/iam/access_control/README.md#iam-credentials-report)
- [IAM Roles for Services](./contents/iam/access_control/README.md#iam-roles-for-services)
- [Amazon STS - Seurity Token Service](./contents/iam/sts/README.md#amazon-sts-seurity-token-service)
- [Sample SCP](./contents/iam/sts/README.md#sample-scp)
- [Instance Metadata Service (IMDS)](./contents/iam/instance_metadata_service_imds/README.md#aws-ec2-instance-metadata-service-(imds))
- [IAM in S3](./contents/iam/s3/README.md#iam-in-s3)
  - [How Authorization Works in Amazon S3](./contents/iam/s3/README.md#how-authorization-works-in-amazon-s3)
  - [Object Operation Request example: 3 accounts](./contents/iam/s3/README.md#object-operation-request-example:-3-accounts)
  - [S3 Canned ACL – Deep Dive](./contents/iam/s3/README.md#s3-canned-acl–deep-dive)
  - [Bucket Policy – Restrict Public IP Address](./contents/iam/s3/README.md#s3-bucket-policy–restrict-public-ip-address)
  - [Bucket Policy – Restrict Public IP Address](./contents/iam/s3/README.md#s3-bucket-policy–restrict-public-ip-address)
  - [VPC Interface Endpoint for S3](./contents/iam/s3/README.md#vpc-interface-endpoint-for-s3)
  - [Regain Access to Locked S3 Buckets](./contents/iam/s3/README.md#regain-access-to-locked-s3-buckets)
  - [Access Points](./contents/iam/s3/README.md#s3–access-points)
  - [CORS in S3](./contents/iam/s3/README.md#what-is-cors)
- [Cognito](./contents/iam/cognito/README.md)
  - [Cognito User Pools](./contents/iam/cognito/README.md#cognito-user-pools-(cup)–user-features)
  - [Cognito Identity Pools – IAM Roles](./contents/iam/cognito/README.md#cognito-identity-pools–iam-roles)
  - [Cognito User Pools vs Identity Pools](./contents/iam/cognito/README.md#cognito-user-pools-vs-identity-pools)
  - [Cognito User Pool Groups](./contents/iam/cognito/README.md#cognito-user-pool-groups)
- [Identity Federation](./contents/iam/identity/README.md#identity-federation-in-aws)
- [IAM Identity Center](./contents/iam/identity/README.md#aws-iam-identity-center)
- [Active Directory](./contents/iam/active_directory/README.md#active-directory)

### Data Protection

- [Encryption](./contents/data_protection/README.md#why-encryption?)
- [CloudHSM](./contents/cloudhsm/README.md#cloudhsm)
- [AWS KMS](./contents/kms/README.md#aws-kms-(key-management-service))
  - [Key Material Origin](./contents/kms/README.md#kms-key-material-origin)
  - [Multi-Region Keys](./contents/kms/README.md#kms-multi-region-keys)
  - [Envelope Encryption](./contents/kms/README.md#envelope-encryption)
  - [KMS Symmetric – API Summary](./contents/kms/README.md#kms-symmetric–api-summary)
  - [Encryption SDK](./contents/kms/README.md#encryption-sdk)
  - [Key Rotation](./contents/kms/README.md#key-rotation)
  - [Asymmetric Encryption](./contents/kms/README.md#asymmetric-encryption)
  - [KMS with SSM Parameter Store](./contents/kms/README.md#kms-with-ssm-parameter-store)
- [Secrest Manager](./contents/secrets_manager/README.md)
  - [Secrets Manager – Secrets Rotation](./contents/secrets_manager/README.md#secrets-manager–secrets-rotation)
  - [KMS with Secrets Manager](./contents/secrets_manager/README.md#kms-with-secrets-manager)
  - [Secrets Manager – Resource Policy](./contents/secrets_manager/README.md#secrets-manager–resource-policy)
- [S3 Encryption](./contents/s3/encryption/README.md#object-encryption)
  - [S3 Encryption Summary](./contents/s3/encryption/README.md#s3-encryption-for-objects-summary)
  - [Default Encryption vs Bucket Policies](./contents/s3/encryption/README.md#amazon-s3–default-encryption-vs.-bucket-policies)
  - [S3 Bucket Key for SSE-KMS Encryption](./contents/s3/encryption/README.md#s3-bucket-key-for-sse-kms-encryption)
  - [S3 Batch – Object Encryption](./contents/s3/encryption/README.md#s3-batch–object-encryption)
- [S3 Glacier Vault Lock](./contents/s3/README.md#s3-glacier-vault-lock)
  - [Vault Lock Process](./contents/s3/README.md#vault-lock-process)
- [Moving between Storage Classes](./contents/s3/README.md#moving-between-storage-classes)
- [S3 Lifecycle Rules](./contents/s3/README.md#lifecycle-rules)
- [Replication (CRR & SRR)](./contents/s3/README.md#replication-(crr-&-srr))
- [Load Balancing](./contents/load_balancing/README.md#load-balancing)
  - [Network Load Balancer (v2)](./contents/load_balancing/README.md#network-load-balancer-(v2))
  - [Sticky Sessions (Session Affinity)](./contents/load_balancing/README.md#sticky-sessions-(session-affinity))
  - [SSL/TLS - Basics](./contents/load_balancing/README.md#ssl/tls-basics)
  - [Elastic Load Balancer](./contents/load_balancing/README.md#elastic-load-balancer)
    - [### SSL – Server Name Indication (SNI)](./contents/load_balancing/README.md#elastic-load-balancer)
    - [HTTPS/SSL Listener – Security Policy](./contents/load_balancing/README.md#https/ssl-listener–security-policy)
    - [NLB - SSL/TLS & HTTPS](./contents/load_balancing/README.md#nlb-ssl/tls-&-https)
- [AWS Certificate Manager (ACM)](./contents/acm/README.md#aws-certificate-manager-(acm))
  - [ACM – Good to know](./contents/acm/README.md#acm–good-to-know)
  - [AWS Private Certificate Authority (CA)](./contents/acm/README.md#aws-private-certificate-authority-(ca))
  - [Validation Techniques](./contents/acm/README.md#validation-techniques)
  - [Process to Manually Create a Certificate](./contents/acm/README.md#process-to-manually-create-a-certificate)
  - [Monitor Expired Imported Certificates](./contents/acm/README.md#monitor-expired-imported-certificates)
- [AWS Backup](./contents/data_protection/README.md#aws-backup)
  - [Vault Lock](./contents/data_protection/README.md#vault-lock)
- [Amazon Data Lifecycle Manager](./contents/data_protection/README.md#amazon-data-lifecycle-manager)
- [Nitro Enclaves](./contents/data_protection/README.md#nitro-enclaves)

### Management and Security Governance

- [AWS Organizations](./contents/organizations_and_accout_tower/README.md#aws-organizations)
  - [SCP Hierarchy](./contents/organizations_and_accout_tower/README.md#scp-hierarchy)
  - [SCP Examples Blocklist and Allowlist strategies](./contents/organizations_and_accout_tower/README.md#scp-examples-blocklist-and-allowlist-strategies)
  - [Reserved Instances](./contents/organizations_and_accout_tower/README.md#reserved-instances)
  - [IAM Policies](./contents/organizations_and_accout_tower/README.md#iam-policies)
  - [Tag Policies](./contents/organizations_and_accout_tower/README.md#tag-policies)
- [AWS Control Tower](./contents/organizations_and_accout_tower/README.md#aws-control-tower)
  - [Account Factory](./contents/organizations_and_accout_tower/README.md#account-factory)
  - [Detect and Remediate Policy Violations](./contents/organizations_and_accout_tower/README.md#detect-and-remediate-policy-violations)
  - [Guardrails Levels](./contents/organizations_and_accout_tower/README.md#guardrails-levels)
- [AWS Config](./contents/config_cost_explorer/README.md#aws-config)
  - [Config Rules](./contents/config_cost_explorer/README.md#config-rules)
  - [Config Resource](./contents/config_cost_explorer/README.md#config-resource)
  - [Remediations](./contents/config_cost_explorer/README.md#remediations)
  - [Use Cases](./contents/config_cost_explorer/README.md#use-cases)
- [Trusted Advisor](./contents/config_cost_explorer/README.md#trusted-advisor)
- [Cost Explorer](./contents/config_cost_explorer/README.md#cost-explorer)
  - [AWS Cost Anomaly Detection](./contents/config_cost_explorer/README.md#aws-cost-anomaly-detection)
  - [Well Architected Framework General Guiding Principles](./contents/config_cost_explorer/README.md#well-architected-framework-general-guiding-principles)
  - [AWS Audit Manager](./contents/config_cost_explorer/README.md#aws-audit-manager)
- [Cloud Formation](./contents/cloudformation/README.md)
  - [Benefits of AWS CloudFormation](./contents/cloudformation/README.md#benefits-of-aws-cloudformation)
  - [CloudFormation Stack Designer](./contents/cloudformation/README.md#cloudformation-stack-designer)
  - [Termination Protection on Stacks](./contents/cloudformation/README.md#termination-protection-on-stacks)
  - [Stack Policies](./contents/cloudformation/README.md#stack-policies)
  - [Dynamic References](./contents/cloudformation/README.md#dynamic-references)
  - [CloudFormation Guard (cfn-guard)](./contents/cloudformation/README.md#cloudformation-guard-(cfn-guard))
- [AWS Service Catalog](./contents/cloudformation/README.md#aws-service-catalog)
- [Direct Connect (DX)](./contents/direct_connect/README.md)
  - [Direct Connect Diagram](./contents/direct_connect/README.md#direct-connect-diagram)
  - [Gateway](./contents/direct_connect/README.md#gateway)
  - [Encryption](./contents/direct_connect/README.md#encryption)
  - [Site-to-Site VPN connection as a backup](./contents/direct_connect/README.md#site-to-site-vpn-connection-as-a-backup)
- [Elastic Container Registry (ECR)](./contents/ecr/README.md)
  - [Elastic Container Registry (ECR) with KMS](./contents/ecr/README.md#elastic-container-registry-(ecr)-with-kms)
  - [ECR – Image Scanning](./contents/ecr/README.md#ecr–image-scanning)
  - [ECR – Cross-Account Access](./contents/ecr/README.md#ecr–cross-account-access)
  - [Amazon EKS – Logging](./contents/ecr/README.md#amazon-eks–logging)
- [Lambda](./contents/lambda/README.md)
  - [Execution Role (IAM Role)](./contents/lambda/README.md#execution-role-(iam-role))
  - [Lambda Resource Based Policies](./contents/lambda/README.md#lambda-resource-based-policies)
  - [Lambda by default](./contents/lambda/README.md#lambda-by-default)
  - [Lambda in VPC](./contents/lambda/README.md#lambda-in-vpc)
  - [Signer](./contents/signer_and_verified_access/README.md#aws-signer)
- [AWS Verified Access](./contents/signer_and_verified_access/README.md#aws-verified-access)
- [AWS Glue](./contents/glue/README.md)
  - [Convert data into Parquet format](./contents/glue/README.md#convert-data-into-parquet-format)
  - [Glue Data Catalog: catalog of datasets](./contents/glue/README.md#glue-data-catalog:-catalog-of-datasets)
  - [Things to know at a high-level](./contents/glue/README.md#things-to-know-at-a-high-level)
  - [Security](./contents/glue/README.md#security)
  - [Centralized Data Catalog](./contents/glue/README.md#centralized-data-catalog)
- [Amazon WorkSpaces](./contents/other_services/README.md#amazon-workspaces)
  - [Security IP Access Control Groups](./contents/other_services/README.md#security-ip-access-control-groups)
- [Auto Scaling](./contents/other_services/README.md#auto-scaling)
- [EBS Volumes – Data Wiping](./contents/other_services/README.md#ebs-volumes–data-wiping)
- [AWS CloudShell](./contents/other_services/README.md#aws-cloudshell)
- [RDS & Aurora Security](./contents/other_services/README.md#rds-&-aurora-security)
- [EC2 Image Builder](./contents/other_services/README.md#ec2-image-builder)
- [Amazon Redshift](./contents/other_services/README.md#amazon-redshift)
- [DynamoDB – Time To Live (TTL)](./contents/other_services/README.md#dynamodb–time-to-live-(ttl))

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

## Abbrivations

- AMI: Amazon Machine Image
- ECR: Elastic Container Registory
- ECS: Elastic Container Service
- ENI: Elastic Network Interface
- XKS: KMS external key store
- CMK: Customer Master Keys
- DEK: Data Encryption Keys
- HSM: Hardware Security Modules

