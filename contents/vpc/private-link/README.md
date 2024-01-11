# AWS PrivateLink (VPC Endpoint Services)

## Exposing services in your VPC to other VPC

### Option 1: Make it public

- Goes through the public www
- Tough to manage access

![Option 1](./exposing_vpc_to_vpc_option_1.png)

### Option 2: VPC Peering

- Must create many peering relations
- Opens the whole network

![Option 2](./exposing_vpc_to_vpc_option_2.png)

## VPC PrivateLink

- Most secure & scalable way to expose a service to 1000s of VPC (own or other accounts)
- Does not require VPC peering, internet gateway, NAT, route tables.
- Requires a network load balancer (Service VPC) and ENI (Customer VPC) or GWLB
- If the NLB is in multiple Availability Zones, and the ENIs in multiple Availability Zones, the solution is fault tolerant

![Private Link](./private_link.png)

- Available in *Endpoint Services* tab in VPC resource

## AWS Private Link & ECS

Two options to achieve this:

- Private Link -> ENI of consumer VPC (AWS account)
- Private link -> Virtual Private Gateway -> Direct Connect -> Customer Gateway (through VPN connection)

![Private Link with ECS](./private_link_ecs.png)
