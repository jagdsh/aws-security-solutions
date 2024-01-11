# Direct Connect (DX)

- Provides a dedicated private connection from a remote network to your VPC
- Dedicated connection must be setup between your DC and AWS Direct Connect locations
- You need to setup a Virtual Private Gateway on your VPC
- Access public resources (S3) and private (EC2) on same connection
- Use Cases:
  - Increase bandwidth throughput - working with large data sets – lower cost
  - More consistent network experience - applications using real-time data feeds
  - Hybrid Environments (on prem + cloud)
- Supports both IPv4 and IPv6

## Direct Connect Diagram

![Direct Connect](./direct_connect.png)

### Gateway

- If you want to setup a Direct Connect to one or more VPC in many different regions (same account), you must use a Direct Connect Gateway

![DX Gateway](./dx_gateway.png)

### Connection Types

- Dedicated Connections: 1Gbps,10 Gbps and 100 Gbps capacity
  - Physical ethernet port dedicated to a customer
  - Request made to AWS first, then completed by AWS Direct Connect Partners
- Hosted Connections: 50Mbps, 500 Mbps, to 10 Gbps
  - Connection requests are made via AWS Direct Connect Partners
  - Capacity can be added or removed on demand
  - 1, 2, 5, 10 Gbps available at select AWS Direct Connect Partners
- Lead times are often longer than 1 month to establish a new connection

### Encryption

- Data in transit is not encrypted but is private
- AWS Direct Connect + VPN provides an IPsec-encrypted private connection
- Good for an extra level of security, but slightly more complex to put in place

![DX Encryption](./dx_encryption.png)

### Resiliency

High Resiliency for Critical Workloads

![High Resiliency](./resiliency_high.png)

One connection at multiple locations

Maximum Resiliency for Critical Workloads
![Maximum Resiliency](./resiliency_maximum.png)

Maximum resilience is achieved by separate connections terminating on separate devices in more than one location.

### Site-to-Site VPN connection as a backup

![Site to Site VPN](./site_to_site_vpn.png)

- In case Direct Connect fails, you can set up a backup Direct Connect connection (expensive), or a Site-to-Site VPN connection

