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

![Chain of Trust](./dnssec_chain_of_trust.png)

## Routing policy

| Routing Policy |   What it does |
| -------------- | -------------- |
| Simple | Simple DNS response providing the IP address associated with a name |
| Failover | If primary is down (based on health checks), routes to secondary destination |
| Geolocation | Uses geographic location you’re in (e.g. Europe) to route you to the closest region |
| Geoproximity | Routes you to the closest region within a geographic area
| Latency | Directs you based on the lowest latency route to resources
| Multivalue answer | Returns several IP addresses and functions as a basic load balancer
| Weighted | Uses the relative weights assigned to resources to determine which to route to |

## Amazon Route 53 Record Types

- A (address record)
- AAAA (IPv6 address record)
- CNAME (canonical name record)
- Alias (an Amazon Route 53-specific virtual record)
- CAA (certification authority authorization)
- MX (mail exchange record)
- NAPTR (name authority pointer record)
- NS (name server record)
- PTR (pointer record)
- SOA (start of authority record)
- SPF (sender policy framework)
- SRV (service locator)
- TXT (text record)

## CNAME vs Alias

| CNAME | Records Alias Records |
| -------------- | -------------- |
| Route 53 charges for CNAME queries | Route 53 doesn’t charge for alias queries to AWS resources
| You can’t create a CNAME record at the top node of a DNS namespace (zone apex) | You can create an alias record at the zone apex (however you can’t route to a CNAME at the zone apex) |
| A CNAME record redirects queries for a domain name regardless of record type | Route 53 follows the pointer in an alias record only when the record type also matches |
| A CNAME can point to any DNS record that is hosted anywhere | An alias record can only point to a CloudFront distribution, Elastic Beanstalk environment, ELB, S3 bucket as a static website, or to another record in the same hosted zone that you’re creating the alias record in |
| A CNAME record is visible in the answer section of a reply from a Route 53 DNS server | An alias record is only visible in the Route 53 console or the  Route 53 API |
| A CNAME record is followed by a recursive resolver | An alias record is only followed inside Route 53. This means that both the alias record and its target must exist in Route 53
