# AWS WAF – Web Application Firewall

- Protects your web applications from common web exploits (Layer 7) (HTTP Layer)
- Deploy on Application Load Balancer (localized rules)
- Deploy on API Gateway (rules running at the regional or edge level)
- Deploy on CloudFront (rules globally on edge locations)
  - Used to front other solutions: CLB, EC2 instances, custom origins, S3 websites
- Deploy on AppSync (protect your GraphQL APIs)
- WAF is not for DDoS protection
- Define Web ACL (Web Access Control List):
  - Rules can include IP addresses, HTTP headers, HTTP body, or URI strings
  - Protects from common attack - SQL injection and Cross-Site Scripting (XSS)
  - Size constraints, Geo match
  - Rate-based rules (to count occurrences of events)
- Rule Actions: Count | Allow | Block | CAPTCHA

## Managed Rules

- Library of over 190 managed rules
- Ready-to-use rules that are managed by AWS and AWS Marketplace Sellers
- Baseline Rule Groups – general protection from common threats
- AWSManagedRulesCommonRuleSet, AWSManagedRulesAdminProtectionRuleSet, …
- Use-case Specific Rule Groups – protection for many AWS WAF use cases
  - AWSManagedRulesSQLiRuleSet, AWSManagedRulesWindowsRuleSet,
    AWSManagedRulesPHPRuleSet, AWSManagedRulesWordPressRuleSet, …
- IP Reputation Rule Groups – block requests based on source (e.g., malicious IPs)
  - AWSManagedRulesAmazonIpReputationList, AWSManagedRulesAnonymousIpList
- Bot Control Managed Rule Group – block and manage requests from bots
  - AWSManagedRulesBotControlRuleSet

## WAF - Web ACL – Logging

- You can send your logs to an:
- Amazon CloudWatch Logs log group – 5 MB per second
- Amazon Simple Storage Service (Amazon S3) bucket – 5 minutes interval
- Amazon Kinesis Data Firehose – limited by Firehose quotas

![WAF ACL Logging](./waf_acl_logging.png)

## Solution Architecture – Enahnce CloudFront Origin Security with AWS WAF & AWS Secrets Manager

![Architecture](./solution_architecture_cf_waf_secrets_manager.png)

# AWS Shield

## Protect from DDoS attack

- DDoS: Distributed Denial of Service – many requests at the same time
• AWS Shield Standard:
  -  Free service that is activated for every AWS customer
  - Provides protection from attacks such as SYN/UDP Floods, Reflection attacks and other layer 3/layer 4 attacks
• AWS Shield Advanced:
  - Optional DDoS mitigation service ($3,000 per month per organization)
  - Protect against more sophisticated attack on Amazon EC2, Elastic Load Balancing (ELB), Amazon CloudFront, AWS Global Accelerator, and Route 53
  - 24/7 access to AWS DDoS response team (DRP)
  - Protect against higher fees during usage spikes due to DDoS
  - Shield Advanced automatic application layer DDoS mitigation automatically creates, evaluates and deploys AWS WAF rules to mitigate layer 7 attacks