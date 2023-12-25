# Security Groups & NACL

- NCAL are stateless and SG are stateful
- First it goes through NACL and then Security Group
- Since SG is stateful if inboud is allowed then outbound will also be allowed

Inbound Request E.g:
![SG and NCAL Inbound Rule](./sg_nacl_inbound_request.png)

OutBound Request E.g:
![SG and NCAL Outbound Rule](./sg_nacl_outbound_request.png)

## Network Access Control list (NACL)

- NACL are like a firewall which control traffic from and to subnets
- One NACL per subnet, new subnets are assigned the Default NACL
- You define NACL Rules:
- Rules have a number (1-32766), higher precedence with a lower number
- First rule match will drive the decision
- Example: if you define #100 ALLOW 10.0.0.10/32 and #200 DENY 10.0.0.10/32, the IP address will be allowed because 100 has a higher precedence over 200
- The last rule is an asterisk (*) and denies a request in case of no rule match
- AWS recommends adding rules by increment of 100
- Newly created NACLs will deny everything
- NACL are a great way of blocking a specific IP address at the subnet level

![NACL](./nacl.png)

### Default NACL

- Accepts everything inbound/outbound with the subnets it’s associated with
- Do NOT modify the Default NACL, instead create custom NACLs

![Default NACL](./default_nacl.png)

## Ephemeral Port

- For any two endpoints to establish a connection, they must use ports
- Clients connect to a defined port, and expect a response on an ephemeral port
- Different Operating Systems use different port ranges, examples:
  - IANA & MS Windows 10 -> 49152 – 65535
  - Many Linux Kernels -> 32768 – 60999

![Ephemeral Port](./ephemeral_ports.png)

## NACL with Ephemeral Ports

![NCAL with Ephemeral Port](./nacl_with_ephemeral_port.png)

[VPC & NACL](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html)

## NACL rules for each target subnets CIDR

## Security Group vs. NACLs

| Security Group | NACL |
| :------------- | :---- |
| Operates at the instance level | Operates at the subnet level |
| Supports allow rules only | Supports allow rules and deny rules |
|  Stateful: return traffic is automatically allowed, regardless of any rules | Stateless: return traffic must be explicitly allowed by rules (think of ephemeral ports)|
| All rules are evaluated before deciding whether to allow traffic | Rules are evaluated in order (lowest to highest) when deciding whether to allow traffic, first match wins |
| Applies to an EC2 instance when specified by someone | Automatically applies to all EC2 instances in the subnet that it’s associated with |


[NACL Example](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html)

## Security Groups – Outbound Rules

## Managed Prefix List

## Security Groups – Extras

## Network topologies can become complicated

