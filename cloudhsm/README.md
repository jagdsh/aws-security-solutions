# CloudHSM

- KMS => AWS manages the software for encryption
- CloudHSM => AWS provisions encryption hardware
- Dedicated Hardware (HSM = Hardware Security Module)
- You manage your own encryption keys entirely (not AWS)
- HSM device is tamper resistant, FIPS 140-2 Level 3 compliance
- Supports both symmetric and asymmetric encryption (SSL/TLS keys)
- No free tier available
- Must use the CloudHSM Client Software
- Redshift supports CloudHSM for database encryption and key management
- Good option to use with SSE-C encryption

## CloudHSM Diagram

![CloudHSM](./cloudhsm.png)

IAM permissions:
• CRUD an HSM Cluster

CloudHSM Software:
• Manage the Keys
• Manage the Users

## CloudHSM – High Availability

- CloudHSM clusters are spread across Multi AZ (HA)
- Great for availability and durability

![CloudHSM High Availability](./cloudhsm_high_availability.png)

## CloudHSM vs. KMS

| Feature                    | AWS KMS                                                         | AWS CloudHSM                                                              |
| -------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------- |
| Tenancy                    | Multi-Tenant                                                    | Single-Tenant                                                             |
| Standard                   | FIPS 140-2 Level 3                                              | FIPS 140-2 Level 3                                                        |
| Master Keys                | • AWS Owned Keys • AWS Managed Keys • Customer Managed KMS Keys | Customer Managed CMK                                                      |
| Key Types                  | • Symmetric • Asymmetric • Digital Signing                      | • Symmetric • Asymmetric • Digital Signing & Hashing                      |
| Key Accessibility          | Accessible in multiple AWS regions KMS Key Replication          | • Deployed and managed in a VPC • Can be shared across VPCs (VPC Peering) |
| Cryptographic Acceleration | None                                                            | • SSL/TLS Acceleration • Oracle TDE Acceleration                          |
| Access & Authentication    | AWS IAM                                                         | You create users and manage their permissions                             |
| High Availability          | AWS Managed Service                                             | Add multiple HSMs over different AZs                                      |
| Audit Capability           | • CloudTrail • CloudWatch                                       | • CloudTrail • CloudWatch • MFA support                                   |
| Free Tier                  | Yes                                                             | No                                                                        |

## CloudHSM – Integration with AWS and 3rd Party Services

- Integration with AWS Services
  - Through integration with AWS KMS
  - Configure KMS Custom Key Store with CloudHSM
  - Example: EBS, S3, RDS, …
  - Supports RDS Oracle TDE (through KMS)
- Integration with 3rd Party Services
  - Allows creating and storing keys in CloudHSM
  - Use cases: SSL/TLS Offload, Windows Server Certificate Authority (CA), Oracle TDE, Microsoft SignTool, Java Keytool

![CloudHSM Integration](./cloudhsm_integration.png)

## CloudHSM – Sharing Cluster Across-Accounts

- You can share the private subnets a CloudHSM clusters resides in using AWS RAM
- You CANNOT share the CloudHSM cluster itself
- Share VPC Subnets with entire Organization, specific OUs, or AWS accounts
- Note: configure CloudHSM Security Group to allow traffic from clients

![CloudHSM Sharing Cluster](./cloudhsm_sharing_across_account.png)
