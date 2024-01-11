# Infrastructure and Security techniques

## Bastion host

![Bastion Host](./visual-referencesbastion_host.png)

- We can use a Bastion Host to SSH into our private EC2 instances
- The bastion is in the public subnet which is then connected to all other private subnets
- Bastion Host security group must allow inbound from the internet on port 22 from restricted CIDR, for example the public CIDR of your corporation
- Security Group of the EC2 Instances must allow the Security Group of the Bastion Host, or the private IP of the Bastion host
  - the original EC2 instance does not have internet access
  - When connecting with original EC2 instance we should use its Key pair pem file

## Site to Site VPN

![Site to Site VPN](./visual-referencessite_to_site_vpn.png)

- Virtual Private Gateway (VGW) => *AWS Side*
  - VPN concentrator on the AWS side of the VPN connection
  - VGW is created and attached to the VPC from which you want to create the Site-to-Site VPN connection
  - Possibility to customize the ASN (Autonomous System Number)

- Customer Gateway (CGW) => *On Premises side*
  - Software application or physical device on customer side of the VPN connection
  - [Reference for CGW](https://docs.aws.amazon.com/vpn/latest/s2svpn/your-cgw.html#DevicesTested)

- Nat Device
  - If it’s behind a NAT device that’s enabled for NAT traversal (NAT-T), use the public IP address of the NAT device

## Site to Site VPN connections

![VPN Connections](./visual-referencessite_to_site_vpn_connections.png)

- Customer Gateway Device (On-premises)
  - What IP address to use?
  - Public Internet-routable IP address for your Customer Gateway device
  - If it’s behind a NAT device that’s enabled for NAT traversal (NAT-T), use the public IP address of the NAT device
- Important step: enable Route Propagation for the Virtual Private Gateway in the route table that is associated with your subnets
- If you need to ping your EC2 instances from on-premises, make sure you add the ICMP protocol on the inbound of your security groups

## VPN Cloudhub

![CloudHub](./visual-referencessite_to_site_cloud_hub.png)

- Provide secure communication between multiple sites, if you have multiple VPN connections
- Low-cost hub-and-spoke model for primary or secondary network connectivity between different locations (VPN only)
- It’s a VPN connection so it goes over the public Internet
- To set it up, connect multiple VPN connections on the same VGW, setup dynamic routing and configure route tables

## Client VPN

![Client VPN](./visual-references/client_vpn.png)

- Connect from your computer using OpenVPN to your private network in AWS and on-premises
- Allow you to connect to your EC2 instances over a private IP (just as if you were in the private VPC network)
- Goes over public Internet

## Client VPN Authentication Type

![Client VPN Authentication](./client_vpn_authentication.png)

- Active Directory Authentication
  - Authenticate against Microsoft Active Directory (User-Based)
  - AWS Managed Microsoft AD or on-premises AD through AD Connector
  - Supports MFA
- Mutual Authentication
  - Use certificates to perform the authentication (Certificate-Based)
  - Must upload the server certificate to AWS Certificate Manager
  - One client certificate for each user (recommended)
- Single Sign-On (supports IAM Identity Center / AWS SSO)
  - Authenticate against SAML 2.0-based identity providers (User-based)
  - Establish trust relationship between AWS and the identity provider
  - Only one identity provider at a time

## VPC Peering

![VPC Peering](./visual-references/vpc_peering.png)

- Privately connect two VPCs using AWS’ network
- Make them behave as if they were in the same network
- Must not have overlapping CIDRs
- VPC Peering connection is NOT transitive (must be established for each VPC that need to communicate with one another)
- You must update route tables in each VPC’s subnets to ensure EC2 instances can communicate with each other

![VPC Peering](./visual-references/vpc_peering_flow_diagram.png)

- You can create VPC Peering connection between VPCs in different AWS accounts/regions
- You can reference a security group in a peered VPC (works cross accounts – same region)

![VPC Peering Security Group](./visual-references/vpc_peering_security_group.png)

## DNS Resolution in VPC

- DNS Resolution (enableDnsSupport)
  - Decides if DNS resolution from Route 53 Resolver server is supported for the VPC
  - True (default): it queries the Amazon Provider DNS Server at 169.254.169.253 or the reserved IP address at the base of the VPC IPv4 network range plus two (.2)

![DNS Resolution](./visual-references/dns_resolution.png)

- DNS Hostnames (enableDnsHostnames)
  - By default,
    - True => default VPC
    - False => newly created VPCs
  - Won’t do anything unless enableDnsSupport=true
  - If True, assigns public hostname to EC2 instance if it has a public IPv4

![DNS Resolution Hostnames](./visual-references/dns_resolution_hostnames.png)

- If you use custom DNS domain names in a Private Hosted Zone in Route 53, you must set both these attributes (enableDnsSupport & enableDnsHostname) to true.

![DNS Resolution Custom DNS domain](./visual-references/dns_resolution_custom_dns_domain.png)
