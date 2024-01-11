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

![Reference Architecture](./SH_overview.png)

### Supported Security standards

- CIS AWS Foundations
- PCI DSS
- AWS Foundational Security Best Practices

## Security Hub <=> Guard Duty

- Guard Duty will send findings to Security Hub
- Enabling Security Hub automatically enables guard duty, Findings are send using AWS Security Findings format (ASFF).
- Archieving is Guard Duty will not automatically archive finding in Security Hub.

![Format of the message send](./guard_duty_finding_type_asff_type.png)

## Security Hub <=> Service Integrations

- Since it recieves all the findings from different services, it is called Security Hub
- To deal with the findings there are independent service which takes care

![Security is a proxy of the findings](./SH_recieve_and_forward_findings.png)

## Security Hub <=> 3rd Party Integrations

- SH can recieve and send findings, from third party integrators.
- You can enable the integration in the AWS Console, selecting which 3rd party integration should be used.

![Security 3rd Party Integrations](./SH_3rd_integrations.png)

## Security Hub - Findings

- SH findings which are in ASFF format will be displayed in table in the SH dashboard.
- Each finding will be deleted ater 90 days if no action is taken.
- You can do an action on the finding by clicking on it and navigating the different status.

![Security Hub findings](./SH_finding_actions.png)

## Security Hub - Insights
  
- Collection of related findings that identifies a security area that requires attention and intervention
  - e.g: Insight points out poor security practices in EC2 instances. This are send as findings to SH dashboard
- Any findings across finding provider can be tracked
- Built-In Managed Insights: We can have custom insights
- We can have our own custom Insights to track practices specific to our environment

## Security Hub - Custom Action

- Integrate every finding or insights from systems to EventBridge with event type "Security Hub Findings - Custom Action"

![Security Hub Actions arch 1](./SH_custom_action_architecture_1.png)

- Amazon Macie sends a findng that S3 bucket is exposing some sensitive data
- Workflow AWS SH can aggregate all the findings and send it to administrator SH deployed in other AWS account
- Here once the finding is caught it can be used to trigger a lambda which remediates the issue by removing or masking the sensitive data that is exposed.
- And it can also resolve in the finding in the member account Security Hub.

![Security Hub Actions arch 2](./SH_custom_action_architecture_2.png)

- The steps to follow to resolve a finding is
  - Detect
  - Ingest
  - Remediate
    - Using Lambda step functions it can be achived lot of results
    - Remediation can also be deligated to the member account using SSM automation
  - Log

## Security Hub - Amazon Macie

- Amazon Macie discovers sensitive data using machine learning and pattern matching, provides visibility into data security risks, and enables automated protection against those risks **specifically for S3 buckets or S3 Objects**

### Use Cases

- Strengthen your data security posture
- Discover sensitive data for compliance
- Protect sensitive data during migration
- Increase visibility for business-critical data
