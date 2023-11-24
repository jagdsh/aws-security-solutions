# What is it?

Intelligent Threat discovery to protect your AWS Account

### Few points to remember
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



## Trusted and Threat IPs
 -   Works only for Public IP address
 - Trusted IPs - Does not generate findings for this IPs
    - IP Address and CIDR range
 - Threat IP
    - list of know malicious ips and CIDR range
    - It can be integrated to 3rd Party intelligence or we can maintain as custom


## Supression Rules
 - Set criteria for automatically filter and archieve the new findings
    - Example: low-value findings, false-positive findings, or threats you don’t intend to act on
 - Suppressed findings are NOT sent to Security Hub, S3, Detective, or EventBridge 
    - But we can still find in Archieve
 - Example Supression Rules
    - Finding type: Recon:EC2/Portscan Instance image ID: ami-999999999
        > Any portscan findings on the image will be archieved
    - Finding type: UnauthorizedAccess:EC2/SSHBruteForce Instance tag value: devops
        > Any bruteforce findings on the instance whic is tagged as `devops` will be archieved


## Troubleshooting
 Problem: GuardDuty is activated but it didn’t generate any DNS based findings

 > Reason: GuardDuty only processes DNS logs if you use the default VPC DNS resolver. All other types of DNS resolvers won't generated DNS based findings.

 > Reason: If GuardDuty is suspended or disabled, then no finding types aregenerated

## Best Practices
 - To enable GuardDuty even if Regions you don’t use, so that we can prevent any suspicious activity when not monitored.
