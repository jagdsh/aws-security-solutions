# Transit Gateway

## Network topologies can become complicated

![Complicated Network Topology](./topology_complicated.png)

## Solution to this is Transit Gateway

- For having transitive peering between thousands of VPC and on-premises, hub-and-spoke (star) connection
- Regional resource, can work cross-region
- Share cross-account using Resource Access Manager (RAM)
- You can peer Transit Gateways across regions
- Route Tables: limit which VPC can talk with other VPC
- Works with Direct Connect Gateway, VPN connections
- Supports ${\color{yellow}IP Multicast}$ (not supported by any other AWS service)

![Transit Gateway](./transite_gateway.png)

## Site to Site VPN ECMP

- ECMP = Equal-cost multi-path routing
- Routing strategy to allow to forward a packet over multiple best path
- Use case: create multiple Site-to-Site VPN connections to increase the bandwidth of your connection to AWS

![Site to Site VPN](./site-to-site-map.png)

## Throughput with ECMP

[VPN To Private Gateway](./throughput_vpn_to_private_gateway.png)

[VPN To Transit Gateway](./throughput_vpn_to_transit_gateway.png)

## Share Direct Connect between multiple accounts

![Transit Gateway between Accounts](./gateway_between_accounts.png)
