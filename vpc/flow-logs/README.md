# VPC Flow logs

![VPC Flow Logs](./vpc_flow_logs.png)

- Capture information about IP traffic going into your interfaces:
  - VPC Flow Logs
  - Subnet Flow Logs
  - Elastic Network Interface (ENI) Flow Logs
- Helps to monitor & troubleshoot connectivity issues
- Flow logs data can go to S3, CloudWatch Logs, and Kinesis Data Firehose
- Captures network information from AWS managed interfaces too: ELB, RDS, ElastiCache, Redshift, WorkSpaces, NATGW, Transit Gateway.

## VPC FLow Creation

- You should be explicitlye create VPC flow logs for VPC in the configuration
- While creation following attrbutes are asked
  - Filter => Accept / Reject / All
  - Maximum aggregation Interval => 10 / 1 min
  - Destination => Cloudwatch / S3 / Kensis Firehouse (same or different account)
    - Cloudwatch -> log group and ${\color{yellow}IAM role with resource based permission}$
    - S3 -> S3 bucket ARN

## Flow Logs Syntax

![Syntax](./flow_logs_syntax.png)

- srcaddr & dstaddr – help identify problematic IP
- srcport & dstport – help identity problematic ports
- Action – success or failure of the request due to Security Group / NACL
- Can be used for analytics on usage patterns, or malicious behavior
- Query VPC flow logs using Athena on S3 or CloudWatch Logs Insights
- Flow Logs examples: https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-records-examples.html

## Troubleshoot SG & NACL issues

${\color{yellow}Look at the *ACTION* field}$

### Incoming Requests

- Networks are stateless and SG are stateful

- Inbound REJECT => NACL or SG
- Inbound ACCEPT, Outbound REJECT => NACL
![Incoming Request](./incoming_request.png)

### Outgoing Requests

- Outbound REJECT => NACL or SG
• Outbound ACCEPT, Inbound REJECT => NACL
![Outgoing Request](./outgoing_request.png)

## Architectures

![Possible Architcture](./architectures.png)

## Traffic not captured

**[Troubleshooting]**:

- Traffic to Amazon DNS server (custom DNS server traffic is logged)
- Traffic for Amazon Windows license activation
- Traffic to and from 169.254.169.254 for EC2 instance metadata
- Traffic to and from 169.254.169.123 for Amazon Time Sync service
- DHCP traffic
- Mirrored traffic
- Traffic to the VPC router reserved IP address (e.g., 10.0.0.1)
- Traffic between VPC Endpoint ENI and Network Load Balancer ENI