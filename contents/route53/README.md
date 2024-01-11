# Route 53

## DNS Query Logging

- Log information about public DNS queries Route 53 Resolver receives
- Only for Public Hosted Zones
- Logs are sent to CloudWatch Logs only

## Resolver Query Logging

- Logs all DNS queries…
  - Made by resources within a VPC (EC2, Lambda, etc…)
  - From on-premises resources that are using Resolver Inbound Endpoints
  - Leveraging Resolvers Outbound Endpoints
  - Using Resolver DNS Firewall
- Can send logs to CloudWatch Logs, S3 bucket, or Kinesis Data Firehose
- Configurations can be shared with other AWS Accounts using AWS Resource Access Manager (AWS RAM)

## DNS Security Extensions (DNSSEC)

- A protocol for securing DNS traffic, verifies DNS data integrity and origin
- Works only with Public Hosted Zones
- Route 53 supports both DNSSEC for Domain Registration and Signing
- DNSSEC Signing
  - Validate that a DNS response came from Route 53 and has not been tampered with
  - Route 53 cryptographically signs each record in the Hosted Zone
  - Two Keys:
    - Managed by you: Key-signing Key (KSK) – based on an asymmetric CMK in AWS KMS
    - Managed by AWS: Zone-signing Key (ZSK)
- When enabled, Route 53 enforces a TTL of one week for all records in the Hosted Zone (records that have TTL less than one week are not affected)

### DNS Poisoning (Spoofing)

> Web Server
> (example.com)
> (IP: 9.10.11.12)

- DNS works on UDP protocol which makes it easy to hack
- There is no cryptographic DNS verification process

![DNS Poisoning - Spoofing](./dnssec_dns_poisoning.png)

### Enable DNSSEC on a hosted zone

- Step 1 – Prepare for DNSSEC signing
  - Monitor zone availibility (through customer feedback)
  - Lower TTL for records (recommended 1 hour)
  - Lower SOA minimum for 5 minutes
- Step 2 – Enable DNSSEC signing and create a KSK
  - Enable DNSSEC in Route 53 for your hosted zone (Console or CLI)
  - Make Route 53 create a KSK in the console and link it to a Customer managed CMK
- Step 3 – Establish chain of trust
  - Create a chain of trust between the hosted zone and the parent hosted zone
  - By creating a Delegation Signer (DS) record in the parent zone
  - It contains a hash of the public key used to sign DNS records
  - Your registrar can be Route 53 or a 3rd party registrar
- Step 4 – (good to have) Monitor for errors using CloudWatch Alarms
  - Create CloudWatch alarms for DNSSECInternalFailure and DNSSECKevSigningKeysNeedingAction

### Chain of Trust

- All the server are DNSSEC aware
- 
![Chain of Trust](./dnssec_chain_of_trust.png)
