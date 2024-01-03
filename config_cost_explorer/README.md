# Config & Cost Explorer

## Config

- Helps with auditing and recording compliance of your AWS resources
- Helps record configurations and changes over time
- Questions that can be solved by AWS Config:
  - Is there unrestricted SSH access to my security groups?
  - Do my buckets have any public access?
  - How has my ALB configuration changed over time?
- You can receive alerts (SNS notifications) for any changes
- AWS Config is a per-region service
- Can be aggregated across regions and accounts
- Possibility of storing the configuration data into S3 (analyzed by Athena)

### Config Rules

- Can use AWS managed config rules (over 75)
- Can make custom config rules (must be defined in AWS Lambda)
  - Ex: evaluate if each EBS disk is of type gp2
  - Ex: evaluate if each EC2 instance is t2.micro
- Rules can be evaluated / triggered:
  - For each config change
  - And / or: at regular time intervals
- AWS Config Rules does not prevent actions from happening (no deny)
- Pricing: no free tier, $0.003 per configuration item recorded per region, $0.001 per config rule evaluation per region

### Config Resource

- View compliance of a resource over time
![Config Compliance](./config_compliance.png)

- View configuration of a resource over time
![Config Configuration](./config_configuration.png)

- View CloudTrail API calls of a resource over time
![Config Cloudtrail](./config_cloudtrail.png)

### Remediations

- Automate remediation of non-compliant resources using SSM Automation Documents
- Use AWS-Managed Automation Documents or create custom Automation Documents
- Tip: you can create custom Automation Documents that invokes Lambda function
- You can set Remediation Retries if the resource is still non-compliant after auto-remediation

![Config Remediation](./config_remediations.png)

### Notifications

- Use EventBridge to trigger notifications when AWS resources are non-compliant
![Config Event Bridge](./config_eventbridge.png)

- Ability to send configuration changes and compliance state notifications to SNS (all events – use SNS Filtering or filter at client-side)
![Config SNS](./config_sns.png)

### Aggregators

![Config Aggregators](./config_aggregators.png)

- The aggregator is created in one central aggregator account
- Aggregates rules, resources, etc… across multiple accounts & regions
- If using AWS Organizations, no need for individual Authorization
- Rules are created in each individual source AWS account
- Can deploy rules to multiple target accounts using CloudFormation StackSets

### Use Cases

- Audit IAM Policies
- Detect if CloudTrail has been disabled
- Detect if EC2 instances are created with unapproved AMIs
- Detect if Security Groups are open to the public
- Detect if Internet Gateway is added to unauthorized VPC
- Detect if EBS volumes are encrypted
- Detect if RDS databases are public

### Example: Expired IAM Keys

![Config Expired Keys](./config_expired_keys.png)

## Trusted Advisor

- No need to install anything – high level AWS account assessment
- Analyze your AWS accounts and provides recommendation on 5 categories
- Cost optimization
- Performance
- Security
- Fault tolerance
- Service limits

![Trusted Advisor](./trusted_advisor.png)

### Support Plans

- 7 CORE CHECKS (Basic & Developer Support plan)
  - S3 Bucket Permissions
  - Security Groups – Specific Ports Unrestricted
  - IAM Use (one IAM user minimum)
  - MFA on Root Account
  - EBS Public Snapshots
  - RDS Public Snapshots
  - Service Limits

- FULL CHECKS (Business & Enterprise Support plan)
  - Full Checks available on the 5 categories
  - Ability to set CloudWatch alarms when reaching limits
  - Programmatic Access using AWS Support API

## Cost Explorer

- Visualize, understand, and manage your AWS costs and usage over time
- Create custom reports that analyze cost and usage data.
- Analyze your data at a high level: total costs and usage across all accounts
- Or Monthly, hourly, resource level granularity
- Choose an optimal Savings Plan (to lower prices on your bill)
- Forecast usage up to 12 months based on previous usage

### Monthly Cost by AWS Service

![Cost Explorer Monthly](./cost_explorer_monthly.png)

### Hourly & Resource Leve

![Cost Explorer Hourly](./cost_explorer_hourly.png)

### Savings Plan Alternative to Reserved Instances

![Cost Explorer Hourly](./cost_explorer_savings.png)

### Forecast Usage

![Cost Explorer Forecast](./cost_explorer_forecast.png)

## AWS Cost Anomaly Detection

- Continuously monitor your cost and usage using ML to detect unusual spends
- It learns your unique, historic spend patterns to detect one-time cost spike and/or continuous cost increases (you don’t need to define thresholds)
- Monitor AWS services, member accounts, cost allocation tags, or cost categories
- Sends you the anomaly detection report with root-cause analysis
- Get notified with individual alerts or daily/weekly summary (using SNS)

![Cost Anamoly](./cost_anamoly.png)

## Well Architected Framework General Guiding Principles

- [Well Architected](https://aws.amazon.com/architecture/well-architected)
- Stop guessing your capacity needs
- Test systems at production scale
- Automate to make architectural experimentation easier
- Allow for evolutionary architectures
  - Design based on changing requirements
- Drive architectures using data
- Improve through game days
  - Simulate applications for flash sale days

### Well Architected Framework 6 Pillars

1) Operational Excellence
2) Security
3) Reliability
4) Performance Efficiency
5) Cost Optimization
6) Sustainability

- They are not something to balance, or trade-offs, they’re a synergy

### AWS Well-Architected Tool

- Free tool to review your architectures against the 6 pillars Well-Architected Framework and adopt architectural best practices
- How does it work?
  - Select your workload and answer questions
  - Review your answers against the 6 pillars
  - Obtain advice: get videos and documentations, generate a report, see the results in a dashboard
- Let’s have a look: [wellarchitected](https://console.aws.amazon.com/wellarchitected)

## AWS Audit Manager

- Assess risk and compliance of your AWS workloads
- Continuously audit AWS services usage and prepare audits
- Prebuilt frameworks include:
  - CIS AWS Foundations Benchmark 1.2.0 & 1.3.0
  - General Data Protection Regulation (GDPR),
  - Health Insurance Portability and Accountability Act (HIPAA)
  - Payment Card Industry Data Security Standard (PCI DSS) v3.2.1
  - Service Organization Control 2 (SOC 2)
- Generates reports of compliance alongside evidence folders
- Integrates with Security Hub, Config, Control Tower, CloudTrail, License Manager
- Run over multi-account via integration with AWS Organizations

![Audit Manager](./audit_manager.png)
