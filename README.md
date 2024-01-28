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

### Highlights

- VPC Flow Logs can be used to capture IP traffic and publish it to Amazon CloudWatch where metric filters can be used to search for specific event patterns such as connection attempts to a specific TCP port
- Automatic provisioning of a secure forensic environment can be achieved using AWS CloudFormation for infrastructure creation and AWS Step Functions to orchestrate the processes (using Lambda) required for forensic analysis
- EC2 instances with Elastic IPs must have the EIPs added to the ingress rules of security groups for connectivity using the public addresses across AZs
- You can collect memory dumps from EC2 instances that are unresponsive using the EC2Rescue CLI with the /offline mode and the device ID specified
- To securely share encrypted snapshots with a separate AWS account, share the snapshot, use a customer managed KMS key, and allow the Decrypt and CreateGrant actions for the target account in the key policy
- If SSH keys for EC2 instances are compromised they can be removed by connecting to each instance and replacing the public key information in the authorized_keys file
- If access key IDs and secret access keys are exposed, you should invalidate any temporary security credentials and delete the access key IDs and secret access keys
- If many access key IDs have been exposed, you can generate a credential report in each account in an organization to identify the users each access key ID belongs to and then rotate those keys
- If temporary security credentials issued by AWS STS are compromised you can revoke all active sessions for the IAM Role
- You can block DDoS attacks where the User-Agent field of the request header has a certain value by using a web ACL with a string match condition that matches that value

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

### Highlights

- Amazon CloudWatch is used for performance, logging, alarms, and
Events
- Amazon EventBridge is based on Amazon CloudWatch Events
- The unified CloudWatch agent can be installed on EC2 instances to collect application log files and stream them to CloudWatch Logs
- The Unified CloudWatch Agent sends system-level metrics for EC2 and on-premises servers
- System-level metrics include memory and disk usage
- An EC2 instance in a private subnet running the unified CloudWatch agent can be configured to send logs to CloudWatch Logs securely via an interface VPC endpoint
- You can also send CloudWatch Logs to S3, Kinesis Data Streams, and Kinesis Data Firehose
- If Lambda fails to write logs to CloudWatch Logs check the function execution role for permissions
- AWS CloudTrail logs API activity for auditing purposes and you must create a trail to store logs indefinitely (90 days otherwise)
- A CloudTrail trail can be configured in the management account of an AWS Organization with logging to a centralized S3 bucket. Administrators in child accounts cannot modify the trail
- Single region AWS CloudTrail trails can be reconfigured to apply to all regions and log API activity to a central Amazon S3 bucket
- To allow delivery of CloudTrail events to an S3 bucket you must ensure the S3 bucket policy grants CloudTrail the s3:PutObject permission and verify that the S3 bucket and prefix defined in CloudTrail exists
- To automatically notify about security group changes use AWS CloudTrail with a CloudWatch Logs log group and use a metric filter to match security group changes and SNS to send notifications
- AWS Config can be used with the managed rule cloudtrail-enabled to check that CloudTrail is enabled. Systems Manager automation can be configured for automatic remediation if the rule returns a non-compliant state
- You can run ad-hoc SQL queries on ELB access logs in an S3 bucket using Amazon Athena
- The restricted-ssh managed rule in AWS Config can be configured to identify security groups that allow unrestricted traffic on port 22 (SSH)
- Amazon Kinesis can be used for ingesting real-time streaming data such as video, audio, application logs, website clickstreams, and IoT telemetry data for machine learning, analytics, and other applications
- Amazon OpenSearch can receive data from Kinesis and analyze that and store that data
- To enable Amazon Detective, you must have enabled Amazon GuardDuty more than 48 hours ago
- You can generate an alert if users add bucket policies by configuring an Amazon EventBridge rule that uses the “AWS API Call via CloudTrail” event source and the “s3:PutBucketPolicy” event pattern

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

### Highlights

- Use separate VPCs to isolate infrastructure, use multiple AZs for high availability, and use subnets to isolate the tiers of your application
- Control network traffic with Security Groups and Network ACLs. Security groups are stateful and NACLs are stateless
- Security groups support allow rules only, are applied at the ENI level, and all rules are evaluated. NACLs apply only to traffic entering / exiting the subnet and are applied in order until an allow or deny is reached
- You can audit security group changes by sending AWS API calls via CloudTrail to Amazon EventBridge and SNS for notifications
- Use private subnets for your instances if they should not be accessed directly from the internet and use VPC interface, gateway endpoints, and AWS PrivateLink to keep traffic private
- Use a NAT gateway or NAT instance for enabling internet access for EC2 instances in private subnets when using IPv4
- Outbound internet connectivity for instances using IPv6 can be enabled using an egress-only internet gateway which will only allow outbound and not inbound traffic
- A Lambda function can be configured to connect to DynamoDB using private IPs by configuring the function in a VPC and using a VPC endpoint for the DynamoDB table
- Lambda functions can be attached to VPCs by configuring the VPC and subnets and you can then use security groups
- For Lambda functions running in a VPC you will need a NAT gateway to reach the internet
- Amazon OpenSearch can be deployed in a VPC, and you will then have a private endpoint (cannot switch to public later)
- Amazon Route 53, AWS Shield, and Elastic Load Balancer can be used to protect EC2 instances against layer 4 DDoS attacks
- Alias records can be used at the zone apex whereas CNAME records can only be used on subdomains
- Internet connectivity to an EC2 instance relies on correct configuration of security groups, network ACLs, route table entries for an internet gateway, and host-based firewall settings (if applicable)
- EC2 instances can be scanned for known software vulnerabilities using Amazon Inspector with the “Common vulnerabilities and exposures” assessment and the “Center for Internet Security (CIS) Benchmarks” assessment
- You can improve security of the instance metadata service by requiring the use of IMDSv2 by setting the “--metadata-options HttpTokens” option to “required”
- EC2 proxy instances and NAT instances must have source/destination checks disabled to be able to forward traffic
- All traffic sent to EC2 instances can be captured for inspection with an intrusion detection appliance by configuring VPC traffic mirroring with a network load balancer
- To connect securely to the CLI on EC2 instances the SSM agent can be used, and the connection made through Session Manager, bypassing SSH ports, and with access controlled by IAM user policy
- Systems Manager Patch Manager can be used to scan EC2 instances, identify vulnerable software, and install patches
- AWS Config can be used to check that EC2 instances have been launched from approved AMIs by using the rule “approved_ami_by_id”
- If health checks are failing for EC2 instances behind an NLB check that the instance security groups, and subnet network ACLs allow traffic from the NLB IP addresses
- To ensure that TLS traffic to an ALB is secure even if the certificate private key is compromised you should create an HTTPS listener with a predefined security policy that supports forward secrecy (FS)
- You can redirect HTTP connections to HTTPS on an ALB by creating both types of listener and a rule that redirects HTTP to HTTPS
- An origin access identity (OAI) can be used to restrict access to an S3 bucket so only CloudFront can access the bucket contents
- You can add a custom header in CloudFront origin settings to restrict access to a specific ALB with a conditional rule on the ALB looking for the header value
- You can use geo restriction in CloudFront to restrict users in specific locations from accessing the content
- To protect against DDoS attacks a static website can be migrated from EC2 to S3 behind a CloudFront distribution with an AWS WAF WebACL
- An AWS WAF web ACL can be associated with an ALB and configured with an IP set match rule statement with a block action to deny incoming requests from specific IP addresses or ranges of addresses
- AWS WAF can be configured with rate-based rules that protect against layer 7 DDoS attacks by putting a temporary block on requests from IP addresses that send excessive requests
- Encryption in transit can be added to an AWS Direct Connect (DX) connection by implementing a Virtual Private Gateway (VGW) and establishing an encrypted site-to-site VPN over the DX connection
- AWS Certificate Manager (ACM) can be used to create SSL/TLS certificates using public or private domain names and subdomains or
wildcards

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
  - [Cognito Identity Pools](./contents/iam/cognito/README.md#cognito-identity-pools)
  - [Cognito User Pools vs Identity Pools](./contents/iam/cognito/README.md#cognito-user-pools-vs-identity-pools)
  - [Cognito User Pool Groups](./contents/iam/cognito/README.md#cognito-user-pool-groups)
- [Identity Federation](./contents/iam/identity/README.md#identity-federation-in-aws)
  - [Web Identity Federation - Without Cognito](./contents/iam/identity/README.md#without-cognito)
  - [Web Identity Federation - With Cognito](./contents/iam/identity/README.md#with-cognito)
  - [web Identity Federation - IAM Policy](./contents/iam/identity/README.md#iam-policy)
- [IAM Identity Center](./contents/iam/identity/README.md#aws-iam-identity-center)
- [Active Directory](./contents/iam/active_directory/README.md#active-directory)

### Highlights

- IAM Roles are used for delegation and are assumed by IAM entities. IAM
policies are attached to roles and define the permissions for the
identities or resources they are associated with
- The trust policy associated with an IAM role determines who is allowed to assume the role
- Identity-based policies are JSON permissions policy documents that control what actions an identity can perform, on which resources, and under what conditions
- Identity-based policies can be applied to users, groups, and roles. Resource-based policies are JSON policy documents that you attach to a resource such as an Amazon S3 bucket
- AWS managed policies are standalone policies that can be attached to multiple users, groups, or roles. Customer managed policies allow you to create your own custom policy permissions to apply to users, groups, or roles
- Groups are collections of users. Users can be members of up to 10 groups and you can assign permissions to all members of the group by attaching IAM policies
- Access authorization to an S3 bucket depends on the union of all the IAM policies, S3 bucket policies, and S3 ACLs that apply
- Permissions boundaries can be configured to specify the maximum available permissions that an identity-based policy can grant to an IAM entity
- S3 bucket policies can be configured to only allow access from the ID of a VPC endpoint
- The condition element in an IAM policy can be used to restrict access based on group membership, IP address etc.
- AWS Secrets Manager can be used for storing encrypted database connection strings and automatically rotating them for some AWS database services
- Automatic rotation of secrets is not available for Systems Manager Parameter Store or other database types in Secrets Manager. You would need to use Lambda to rotate the secrets
- To successfully issue the GetParameter API against secure string parameters in Systems Manager Parameter Store, the EC2 instance must have an IAM role assigned that has decrypt permissions on the KMS key and permissions to retrieve parameters
- To secure RDS database connection credentials store them in AWS Secrets Manager or Systems Manager Parameter Store in an encrypted state. Configure the EC2 instance role with credentials to retrieve the connection credentials
- Automatic rotation is available for Secrets Manager when using specific types of database such as RDS, DocumentDB and RedShift
- Host-based firewalls on EC2 instances may be used when complex rules are required to exceed the limits of security groups and network ACLs
- You can restrict that a KMS key is used only by specific services by using the kms:ViaService condition key and specifying the services that should be allowed to use the key
- To enforce that a role can only be assumed after authentication with MFA add an "aws:MultiFactorAuthPresent : true" condition to the role's trust policy
- You can identify the federated user that terminated an EC2 instance by searching CloudTrail for the TerminateInstances event and taking the IAM Role ARN and then looking for AssumeRoleWithSAML events that used that role
- AWS Organizations enables centralized management and governance of multiple AWS accounts, and you can apply service control policies (SCPs), tag policies, and use consolidated billing
- SCPs attached to member accounts control permissions for all users including the root user
- An explicit deny in an SCP cannot be overridden with an allow statement anywhere beneath it in the hierarchy
- The only way to get around the inheritance of SCP deny statements is to ensure that the SCP is not applied to any OUs with accounts you do not want to apply the deny statement to
- You can use the condition key aws:PrincipalOrgID in policies to require all principals accessing the resource to be from an account in an AWS Organization
- Use * as a principal and aws:PrincipalOrgID as a condition key to restrict to users within the organization
- AWS Control Tower can be used for deploying and managing large numbers of AWS accounts. Control Tower creates a well-architected multi-account baseline known as a landing zone that is based on best practices
- With Control Tower you have preventive guardrails and detective guardrails. Preventive guardrails are based on SCPs and disallow API actions. Detective guardrails are implemented using AWS Config rules and Lambda functions and monitor and govern compliance
- AWS Managed Microsoft AD is a managed implementation of Microsoft Active Directory running on Windows Server 2012 R2. A HA pair of Windows Server 2012 Domain Controllers (DCs) are deployed in your VPC
- AD Connector is used to connect AWS to your on-premises Microsoft Active Directory. You can use it for sign-in to various AWS applications, seamlessly join Windows EC2 instances to the domain, and implement federated sign-in to the AWS management console
- When federating between a corporate IdP and IAM you can use IAM roles to manage access to AWS resources
- Federated sign in to AWS using ADFS and SAML can be used if you keep your identities in an on-premises directory such as Microsoft AD
- Password policies should be configured within Active Directory when using IAM federation
- Password policies can also be configured in IAM user pools and in IAM itself
- AWS SSO can be used for single sign on across many accounts using an existing on-premises identity provider
- Amazon Cognito is used for sign-in and sign-up for mobile applications. User pools are used for creating users or federating to social IdPs and identity pools are used for obtaining temporary, limited-privilege credentials for AWS services

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

### Highlights

- You can create and manage symmetric and asymmetric encryption keys with AWS KMS, and they are protected by hardware security modules (HSMs)
- KMS keys used to be known as “customer master keys” or CMKs and this terminology could still be used in the exam
- KMS keys can only encrypt data up to 4 KB in size and for anything larger you need to create data encryption keys
- AWS managed KMS keys are created, managed, and used on your behalf by an AWS service that is integrated with AWS KMS. You cannot manage these KMS keys, rotate them, or change their key policies
- Automatic rotation of KMS keys generates new key material every year (optional for customer managed keys) and is supported for symmetric keys with key material AWS KMS creates
- You cannot use automatic rotation in the following situations:
  - You are using asymmetric KMS keys
  - You have KMS keys in custom key stores (AWS CloudHSM)
  - You are using KMS keys with imported key material
- Manual rotation is creating a new KMS key with a different key ID . You must then update your applications with the new key ID. You can use an alias to represent a KMS key, so you don’t need to modify your application code
- KMS key policies define management and usage permissions for KMS keys. Multiple policy statements can be combined to specify separate administrative and usage permissions
- When using AWS KMS customer managed keys with KMS key material you can enable automatic rotation for the key material every year. Automatic rotation of AWS managed KMS Keys is every 3 years and cannot be modified
- Temporary permissions to decrypt data encrypted with KMS keys can be provided using KMS grants. You must be granted permissions to use an AWS KMS key for encryption/decryption in the key policy
- If you require a managed service for encryption keys but must be able to immediately delete the keys use AWS KMS with imported key material and then use the DeletelmportedKeyMaterial API to remove the key material if necessary
- Cryptographic erasure within 15 minutes can be achieved using a KMS key with imported key material and deleting the key material if necessary
- An InvalidKeyId error when trying to use an AWS KMS key may indicate that the key is disabled
- AWS CloudHSM should be used if you need hardware based HSMs within a VPC
- If you need to import your own key material into KMS use a default key store and a customer managed KMS key
- RDS databases cannot be encrypted after creation and you must instead take a snapshot, encrypt a copy of the snapshot, and create a new encrypted DB from the encrypted snapshot
- A multi-tier web application with an RDS database can be secured using encrypted EBS volumes, encrypted DB volumes, TLS connections to the DB, and connection strings stored in AWS Secrets Manager (with automatic rotation)
- RDS database instances are configured with an SSL/TLS certificate, and you can download the AWS-provided root certificates for all Regions or specific Regions
- To identify and alert on unencrypted databases use AWS Config with an SNS notification
- Policy variables can be used to specify placeholders in a policy that are replaced with values from the context of the connection request. For example, you can replace the variable with the friendly name of the user making the connection
- The S3 Glacier vault lock operation can be aborted after the initiate vault lock operation
- To secure an S3 bucket with your own keys whilst not managing the encryption process, use server-side encryption with AWS KMS keys and supply your own key material
- Client-side encryption means the entire encryption and decryption takes place outside of AWS and you manage the keys. You can optionally use KMS keys
- Take the following actions to enforce encryption for an S3 bucket:
  - Add a condition to the S3 bucket policy that allows actions if the request meets the condition "aws:SecureTransport": "true"
  - Configure default encryption for the S3 bucket. Add a condition to the S3 bucket policy that denies PUT requests that don’t include the “x-amz-server-side-encryption” header
- Amazon EBS encryption affects:
  - Data at rest inside the volume
  - All data moving between the volume and the instance (in-transit).
  - All snapshots created from the volume
  - All volumes created from those snapshots

- For Amazon EFS, encryption at-rest can be enabled when the file system is created but not after it has been created
- Encryption in-transit can be enabled for Amazon EFS file systems when mounting the file system using the TLS protocol
- The files, deployment packages, and environment variables in a Lambda function are encrypted at rest using an AWS KMS key. AWS recommend that you use Secrets Manager for storing sensitive data instead of environment variables
- To ensure that code written by a developer cannot be deployed to Lambda functions you can use AWS Signer and revoke all versions for the signing profile assigned to the developer
- To verify code integrity when deploying code to Lambda functions use AWS Signer and use IAM policies to enforce that developers can only create functions that have code signing enabled
- Use a VPC endpoint for Kinesis Data Streams to keep the connection private between the VPC and the stream
- Data in Amazon DynamoDB tables is encrypted at rest, and you can choose to select an AWS KMS key or use the default key. Clients connect using published APIs over TLS
- All data flowing across AWS Regions over the AWS global network is automatically encrypted at the physical layer before it leaves AWS secured facilities. All traffic between AZs is also encrypted
- Traffic between instances may be encrypted in some circumstances. The instances must use a supported instance type and be within the same Region and VPC (or in a peered VPC)

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

