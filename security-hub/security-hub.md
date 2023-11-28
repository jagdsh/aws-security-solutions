# What is it?

- Central security tool to manage security across several AWS accounts and automate security checks. 
- Integrated dashboards showing current security and compliance status to quickly take
actions

## What all tools it integrtes to?

Automatically aggregates alerts in predefined or personal findings formats from various AWS services & AWS partner tools:
    - Config
    - GuardDuty
    - Inspector
    - Macie
    - IAM Access Analyzer
    - AWS Systems Manager
    - AWS Firewall Manager
    - AWS Health
    - AWS Partner Network Solutions

### Pre-requistic
 AWS Config Service should be enabled

## Feature

- Cross-Region Aggregation – aggregate findings, insights, and security scores from multiple Regions to a single aggregation Region

- AWS Organizations Integration
    - Manage all accounts in the Organization
    - Security Hub automatically detects new accounts
    - By default, Organization management account is the Security Hub administrator
    - Ability to have a designated Security Hubadministrator from member accounts

- AWS Config must be enabled
    - Security Hub uses AWS Config to perform its security checks
    - Must be enabled on all accounts (Security Hub does NOT manage AWS Config)

For every findings by one of the resources in the Security Hub event is send to Eventbridge and from their it an be shipped to lamda or sqs or sns topics. To analyze the origin of the issue, we can use Amazon Detective. 

![Reference Architecture](./security_hub_overview.png)

### Supported Security standards
- CIS AWS Foundations
- PCI DSS 
- AWS Foundational Security Best Practices 

## Guard Duty <=> Security Hub

Enabling Security Hub automatically enables guard duty, Findings are send using AWS Security Findings format 


