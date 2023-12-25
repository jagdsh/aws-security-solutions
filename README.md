# AWS Security solutions

This is a personal notes capturing various various AWS resources which are helpful in securing application. This repo containsa an list of AWS resources which can be used for detecting, isolating and analyzing.

## Index

### Threat Detection and Incident Response

- [Guard Duty](./guard_duty/README.md)
- [Security Hub](./security-hub/README.md)
- [Amazon Detective](./detective/README.md)
- [Security Testing](./security-testing/README.md)
  - [Penetration Testing](./security-testing/README.md#penetration-testing)
- [Compromised Resources](./compromised-resources/README.md)
  - [EC2](./compromised-resources/README.md#compromised-ec2-instance)
  - [S3](./compromised-resources/README.md#compromised-s3-bucket)
  - [ECS](./compromised-resources/README.md#compromised-ecs-cluster)
  - [Standalone Container](./compromised-resources/README.md#compromised-standalone-container)
  - [RDS](./compromised-resources/README.md#compromised-rds-instance)
  - [AWS Credentials](./compromised-resources/README.md#compromised-aws-credentials)
  - [IAM Role](./compromised-resources/README.md#compromised-iam-role)
  - [AWS Account](./compromised-resources/README.md#compromised-aws-account)
- [EC2 Troubleshooting](./EC2/README.md)
  - [Remediating EC2 Exposed Key Pair](./EC2/README.md#remediating-ec2-exposed-key-pair)
  - [EC2 SSH Connection workflow](./EC2/README.md#ec2-ssh-connection-workflow)
  - [EC2 Serial Console](./EC2/README.md#ec2-serial-console)
  - [Lost SSH EC2 Key Pair - Linux](./EC2/README.md#lost-ssh-ec2-key-pair-linux)
  - [EC2 Rescue tool For Linux](./EC2/README.md#for-linux)
  - [EC2 Rescue tool For Windows](./EC2/README.md#for-windows)
- [IAM Policy](./IAM/README.md)
  - [Access Analyzer](./IAM/README.md#iam-access-analyzer)
  - [Policy Generator](./IAM/README.md#iam-access-analyzer-policy-generation)
- [Policy](./policy/README.md)
  - [Acceptable Use Policy](./policy/README.md#acceptable-use-policy)
  - [AWS Abuse Report](./policy/README.md#aws-abuse-report)

### Security Logging and Monitoring

- [Amazon Inspector](./inspectod/README.md)
- [CloudWatch](./cloudwatch/README.md)
- [Athena](./athena/README.md)
- [CloudTrail](./cloudtrail/README.md)
  - [Summary](./cloudtrail/README.md#summary-monitor-account-activity)
- [Macie](./macie/README.md)
- [S3 Event Notification](./s3/event-notifications/README.md)
- [VPC Flow Logs](./vpc/flow-logs/README.md)
- [VPC Traffic Mirroring](./vpc/traffic-mirroring/README.md)
- [Route 53 Query Logging](./route53/README.md)
- [OpenSearch](./opensearch/README.md)

### Infrastructure and Security

- [Bastion Host](./infrastructure-and-security/README.md#bastion-host)
- [Site to Site VPN](./infrastructure-and-security/README.md#site-to-site-vpn)
- [Client VPN](./infrastructure-and-security/README.md#client-vpn)
- [VPC Peering](./infrastructure-and-security/README.md#vpc-peering)
- [VPC Endpoints](./vpc/endpoints/README.md)
- [Private Link](./vpc/private-link/README.md)

## Appendix

    *Vulnerability* – weakness in a system, system security procedures, internal controls, or implementation that could be exploited

    *Exploit* – code that takes advantage of software vulnerability or security flaws

    *Payload* – a malware that the attacker intends to deliver to the victim

    *Automated Vulnerability Scanner* – a tool that run automated scans of an IT environments to detect vulnerabilities, example : nessus scan (note: you don't need to know that tool for the exam)

    *Common Vulnerabilities and Exposures (CVE)* – a list of publicly disclosed security flaws.

    *Common Vulnerability Scoring System (CVSS)* – a way to produce a numerical score reflecting the severity of a vulnerability or a security flaws
