# What is it?

Intelligent Threat discovery to protect your AWS Account

![Guard Duty](./guard_duty.png)

## Few points to remember

- Uses Machine Learning algorithms, anomaly detection, 3rd party data
- One click to enable (30 days trial), no need to install software
- Input data includes:
  - CloudTrail Events Logs – unusual API calls, unauthorized deployments
    - CloudTrail Management Events – create VPC subnet, create trail,
    - CloudTrail S3 Data Events – get object, list objects, delete object,
  - VPC Flow Logs – unusual internal traffic, unusual IP address
  - DNS Logs – compromised EC2 instances sending encoded data within DNS queries
  - Optional Feature – EKS Audit Logs, RDS & Aurora, EBS, Lambda, S3 Data Events
- Can setup EventBridge rules to be notified in case of findings
- EventBridge rules can target AWS Lambda or SNS
- Can protect against CryptoCurrency attacks (has a dedicated “finding” for it)

## Multi-Account Strategy

- You can manage multiple accounts in GuardDuty
- Associate the Member accounts with the Administrator account
  - Through an AWS Organization
  - Sending invitation through GuardDuty
- Administrator account can:
  - Add and remove member accounts
  - Manage GuardDuty within the associated member accounts
  - Manage findings, suppression rules, trusted IP lists, threat lists
- In an AWS Organization, you can specify a member account as the
Organization’s delegated administrator for GuardDuty

![Guard Duty Multi Account Strategy](./guard_duty_multi_account_strategy.png)

## Findings

- GuardDuty pulls independent streams of data directly from CloudTrail logs (management events, data events), VPC Flow Logs or EKS logs
- Each finding has a severity value between 0.1 to 8+ (High, Medium, Low)
- Finding naming convention ThreatPurpose:ResourceTypeAffected/ThreatFamilyName.DetectionMechanism!Artifact
  - ThreatPurpose – primary purpose of the threat (e.g., Backdoor, CryptoCurrency)
  - ResourceTypeAffected – which AWS resource is the target (e.g., EC2, S3)
  - ThreatFamilyName – describes the potential malicious activity (e.g., NetworkPortUnusual)
  - DetectionMechanism – method GuardDuty detecting the finding (e.g., Tcp, Udp)
  - Artifact – describes a resource that is used in the malicious activity (e.g., DNS)
- You can generate sample findings in GuardDuty to test your automations

### Findings Automated Response

- Findings are potential security issue for malicious events happening in your AWS account
- Automate response to security issues revealed by GuardDuty Findings using EventBridge
- Send alerts to SNS (email, Lambda, Slack, Chime, …)
- Events are published to both the administrator account and the member account that it is originated from

![Findings Automated Response](./guard_duty_findings_automated.png)

### Findings Types

- EC2 Finding Types
  - UnauthorizedAccess:EC2/SSHBruteForce, CryptoCurrency:EC2/BitcoinTool.B!DNS
- IAM Finding Types
  - Stealth:IAMUser/CloudTrailLoggingDisabled, Policy:IAMUser/RootCredentialUsage
- Kubernetes Audit Logs Finding Types
  - CredentialAccess:Kubernetes/MaliciousIPCaller
- Malware Protection Finding Types
  - Execution:EC2/SuspiciousFile, Execution:ECS/SuspiciousFile
- RDS Protection Finding Types
  - CredentialAccess:RDS/AnomalousBehavior.SuccessfulLogin
- S3 Finding Types
  - Policy:S3/AccountBlockPublicAccessDisabled, PenTest:S3/KaliLinux

## GuardDuty Architectures

GuardDuty Architecture 1:

![GuardDuty Architecture 1](./guard_duty_architectures_1.png)

GuardDuty Architecture 2:

![GuardDuty Architecture 2](./guard_duty_architectures_2.png)

## Trusted and Threat IPs

- Works only for public IP addresses
- Trusted IP List
  - List of IP addresses and CIDR ranges that you trust
  - GuardDuty doesn’t generate findings for these trusted lists
- Threat IP List
  - List of known malicious IP addresses and CIDR ranges
  - GuardDuty generates findings based on these threat lists
  - Can be supplied by 3rd party threat intelligence or created custom for you
- In a multi-account GuardDuty setup, only the GuardDuty administrator account can manage those lists

![Trusted IP and Threat IP](./gurad_duty_trusted_ip_threat_ip.png)

## Supression Rules

- Set of criteria that automatically filter and archive new findings
- Example: low-value findings, false-positive findings, or threats you don’t intend to act on
- Can suppress entire findings types or define more granular criteria (e.g., suppress only specific EC2 instances)
- Suppressed findings are NOT sent to Security Hub, S3, Detective, or EventBridge
- Suppressed findings can be still viewed in the Archive

Automatically archive findings when there is a port scan on a specific EC2 instance (e.g., running vulnerability assessment):

Finding type: ${\color{red}Recon:EC2/Portscan}$ Instance image ID: ${\color{red}ami-999999999}$

Automatically archive findings when SSH Brute Force attacks are targeting Bastion Hosts EC2 instances:

Finding type: ${\color{red}UnauthorizedAccess:EC2/SSHBruteForce}$ Instance tag value:${\color{red}devops}$

## GuardDuty didn’t generate any finding types

**[Troubleshooting]**:

- Problem: GuardDuty is activated but it didn’t generate any DNS based findings
- Reason: GuardDuty only processes DNS logs if you use the default VPC DNS resolver. All other types of DNS resolvers won't generated DNS based findings.
- If GuardDuty is suspended or disabled, then no finding types are generated
- Best practice to enabled GuardDuty even if Regions you don’t use

## Best Practices

- To enable GuardDuty even if Regions you don’t use, so that we can prevent any suspicious activity when not monitored.
