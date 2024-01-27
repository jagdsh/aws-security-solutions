# AWS Firewall

## AWS WAF – Web Application Firewall

- Protects your web applications from common web exploits (Layer 7) (HTTP Layer)
- Deploy using Resource Type
  - Deploy on Application Load Balancer (localized rules)
  - Deploy on API Gateway (rules running at the regional or edge level)
  - Deploy on CloudFront (rules globally on edge locations)
  - Used to front other solutions: CLB, EC2 instances, custom origins, S3 websites
  - Deploy on AppSync (protect your GraphQL APIs)
  - Deploy for Amazon Cognito User pools
- <ins>WAF is not for DDoS protection</ins>, it is only used for HTTP protection (Layer 7)
- Define Web ACL (Web Access Control List):
  - Rules can include IP addresses, HTTP headers, HTTP body, or URI strings
  - Protects from common attack - SQL injection and Cross-Site Scripting (XSS)
  - Size constraints, Geo match
  - Rate-based rules (to count occurrences of events)
- Rule Actions: Count | Allow | Block | CAPTCHA
  - Count: To analyze False positive instead of blocking a request
  - CAPTCHA: Client side verificatin before
  - Challenge: Client side challenge token that can be used for particular time default setting is 300 seconds.

### Managed Rules

- Library of over 190 managed rules
- Ready-to-use rules that are managed by AWS and AWS Marketplace Sellers
- Baseline Rule Groups – general protection from common threats
  - AWSManagedRulesCommonRuleSet, AWSManagedRulesAdminProtectionRuleSet,
- Use-case Specific Rule Groups – protection for many AWS WAF use cases
  - AWSManagedRulesSQLiRuleSet, AWSManagedRulesWindowsRuleSet,
    AWSManagedRulesPHPRuleSet, AWSManagedRulesWordPressRuleSet,
- IP Reputation Rule Groups – block requests based on source (e.g., malicious IPs)
  - AWSManagedRulesAmazonIpReputationList, AWSManagedRulesAnonymousIpList
- Bot Control Managed Rule Group – block and manage requests from bots
  - AWSManagedRulesBotControlRuleSet

### WAF - Web ACL – Logging

- You can send your logs to an:
- Amazon CloudWatch Logs log group – 5 MB per second
- Amazon Simple Storage Service (Amazon S3) bucket – 5 minutes interval
- Amazon Kinesis Data Firehose – limited by Firehose quotas

![WAF ACL Logging](./waf_acl_logging.png)

### Solution Architecture – Enhance CloudFront Origin Security with AWS WAF & AWS Secrets Manager

![Architecture](./solution_architecture_cf_waf_secrets_manager.png)

### Match statement

- Match statements compare the web request or its origin against conditions that you provide

| Match Statement | Description |
| ............... |............ |
|Geographic match | Inspects the request's country of origin|
| IP set match | Inspects the request against a set of IP addresses and address ranges |
| Regex pattern set | Compares regex patterns against a specified request component |
| Size constraint | Checks size constraints against a specified request component |
| SQLi attack | Inspects for malicious SQL code in a specified request component |
| String match | Compares a string to a specified request component |
| XSS scripting attack | Inspects for cross-site scripting attacks in a specified request component |

## AWS Shield

### Protect from DDoS attack

- DDoS: Distributed Denial of Service – many requests at the same time from lot of computers in the world
• AWS Shield Standard:
  - Free service that is activated for every AWS customer
  - Provides protection from attacks such as SYN/UDP Floods, Reflection attacks and other layer 3/layer 4 attacks
• AWS Shield Advanced:
  - Optional DDoS mitigation service ($3,000 per month per organization)
  - Protect against more sophisticated attack on Amazon EC2, Elastic Load Balancing (ELB), Amazon CloudFront, AWS Global Accelerator, and Route 53
  - 24/7 access to AWS DDoS response team (DRP)
  - Protect against higher fees during usage spikes due to DDoS
  - Shield Advanced automatic application layer DDoS mitigation automatically creates, evaluates and deploys AWS WAF rules to mitigate layer 7 attacks

### Shield Advanced CloudWatch Metrics

- Helps you to detect if there’s a DDoS attack happening
- DDoSDetected – indicates whether a DDoS event is happening for a specific resource
- DDoSAttackBitsPerSecond – number of bits per second during DDoS event for a specific resource
- DDoSAttackPacketsPerSecond – number of packets per second during a DDoS event for a specific resource
- DDoSAttackRequestsPerSecond – number of requests per second during a DDoS event for a specific resource

## AWS Firewall Manager

- Manage rules in all accounts of an AWS Organization *in specific region not all regions*
- Security policy: common set of security rules
  - WAF rules (Application Load Balancer, API Gateways, CloudFront)
  - AWS Shield Advanced (ALB, CLB, NLB, Elastic IP, CloudFront)
  - Security Groups for EC2, Application Load Balancer and ENI resources in VPC
  - AWS Network Firewall (VPC Level)
  - Amazon Route 53 Resolver DNS Firewall
  - Policies are created at the region level
- Rules are applied to new resources as they are created (good for compliance) across all and future accounts in your Organization

## WAF vs. Firewall Manager vs. Shield

- WAF, Shield and Firewall Manager are used together for comprehensive protection
- Define your Web ACL rules in WAF
- For granular protection of your resources, WAF alone is the correct choice
- If you want to use AWS WAF across accounts, accelerate WAF configuration, automate the protection of new resources, use Firewall Manager with AWS WAF
- Shield Advanced adds additional features on top of AWS WAF, such as dedicated support from the Shield Response Team (SRT) and advanced reporting.
- If prone to frequent DDoS attacks, consider purchasing Shield Advanced

## Network Firewall

- Protect your entire Amazon VPC
- From Layer 3 to Layer 7 protection
- Any direction, you can inspect
  - VPC to VPC traffic
  - Outbound to internet
  - Inbound from internet
  - To / from Direct Connect & Site-to-Site VPN
- Internally, the AWS Network Firewall uses the AWS Gateway Load Balancer
- Rules can be centrally managed cross-account by AWS Firewall Manager to apply to many VPCs

![Network Firewall](./network_firewall.png)

### Network Protection on AWS

- To protect network on AWS, we’ve seen
- Network Access Control Lists (NACLs)
- Amazon VPC security groups
- AWS WAF (protect against malicious requests)
- AWS Shield & AWS Shield Advanced
- AWS Firewall Manager (to manage them across accounts)
- But what if we want to protect in a sophisticated way our entire VPC?

### Fine Grained Controls

- Supports 1000s of rules
- IP & port - example: 10,000s of IPs filtering
- Protocol – example: block the SMB protocol for outbound communications
- Stateful domain list rule groups: only allow outbound traffic to *.mycorp.com or third-party software repo
- General pattern matching using regex
- Traffic filtering: Allow, drop, or alert for the traffic that matches the rules
- Active flow inspection to protect against network threats with intrusion-prevention capabilities (like Gateway Load Balancer, but all managed by AWS)
- Send logs of rule matches to Amazon S3, CloudWatch Logs, Kinesis Data Firehose

### Deployment Architectures - 1

![Deployment Architectures - 2](./network_firewall_deployment_architecture_1.png)

### Deployment Architectures - 2

![Deployment Architectures - 2](./network_firewall_deployment_architecture_2.png)

### Encrypted Traffic

- AWS Network Firewall supports Deep Packet Inspection (DPI) for encrypted traffic Transport Layer Security (TLS)
- It decrypts the TLS traffic, inspects and blocks any malicious content, then re-encrypts the traffic for the destination
- Integrates with AWS Certificate Manager (ACM)

![Encrypted Traffic](./network_firewall_encrypted_traffic.png)

## AWS Best Practices for DDoS Resiliency

### Global Accelerator

- Global Accelerator routes traffic to the closest edge location by using Anycast, and then routes it to the closest regional endpoint over the AWS global network.
- Global Accelerator quickly reacts to changes in network performance to improve your users' application performance.

### Edge Location Mitigation (BP1, BP3)

- BP1 – CloudFront
  - Web Application delivery at the edge
  - Protect from DDoS Common Attacks (SYN floods, UDP reflection…)
- BP1 – Global Accelerator
  - Access your application from the edge
  - Integration with Shield for DDoS protection
  - Helpful if your backend is not compatible with CloudFront
- BP3 – Route 53
  - Domain Name Resolution at the edge
  - DDoS Protection mechanism

![DDoS Edge Location mitigation](./ddos_edge_location_mitigation.png)

### Best pratices for DDoS mitigation

- Infrastructure layer defense (BP1, BP3, BP6)
  - Protect Amazon EC2 against high traffic
  - That includes using Global Accelerator, Route 53, CloudFront, Elastic Load Balancing
- Amazon EC2 with Auto Scaling (BP7)
  - Helps scale in case of sudden traffic surges including a flash crowd or a DDoS attack
- Elastic Load Balancing (BP6)
  - Elastic Load Balancing scales with the traffic increases and will distribute the traffic to many EC2 instances

![Best Practices for DDoS mitigation](./ddos_mitigation.png)

### Application Layer Defense

- Detect and filter malicious web requests (BP1, BP2)
  - CloudFront cache static content and serve it from edge locations, protecting your backend
  - AWS WAF is used on top of CloudFront and Application Load Balancer to filter and block requests based on request signatures
  - WAF rate-based rules can automatically block the IPs of bad actors
  - Use managed rules on WAF to block attacks based on IP reputation, or block anonymous Ips
  - CloudFront can block specific geographies
- Shield Advanced (BP1, BP2, BP6)
  - Shield Advanced automatic application layer DDoS mitigation automatically creates, evaluates and deploys AWS WAF rules to mitigate layer 7 attacks

![Applicaiton Layer Defense](./ddos_application_layer_defense.png)

### Attack surface reduction

- Obfuscating AWS resources (BP1, BP4, BP6)
  - Using CloudFront, API Gateway, Elastic Load Balancing to hide your backend resources (Lambda functions, EC2 instances)
- Security groups and Network ACLs (BP5)
  - Use security groups and NACLs to filter traffic based on specific IP at the subnet or ENI-level
  - Elastic IP are protected by AWS Shield Advanced
- Protecting API endpoints (BP4)
  - Hide EC2, Lambda, elsewhere
  - Edge-optimized mode, or CloudFront + regional mode (more control for DDoS)
  - WAF + API Gateway: burst limits, headers filtering, use API keys

![Attack surface reduction](./ddos_attack_surface_reduction.png)
