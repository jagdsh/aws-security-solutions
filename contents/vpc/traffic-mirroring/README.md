# Traffic Mirroring

- Allows you to capture and inspect network traffic in your VPC
- Route the traffic to *security appliances* that you manage
- Capture the traffic
  - From (Source) – ENIs
  - To (Targets) – an ENI or a Network Load Balancer
- Capture all packets or capture the packets of your interest (optionally, truncate packets)
- Source and Target can be in the same VPC or different VPCs (VPC Peering)
- Use cases: content inspection, threat monitoring, troubleshooting

## Architectures

### Mirror to Network Load Balancer

![Same security Appliance](./mirroring_to_nlb_deployment.png)

- Traffic is distributed across EC2 instances in ASG (same security appliance)

### Mirror to Cloud Packet for different Secuirty Appliances

![Same security Appliance](./mirroring_to_differnet_security_deployment.png)

- All Traffic is sent to multiple EC2 instances with different security appliances

### Distributed Deployment Model

![Distributed Deployment Model](./mirroring_distributed_deployment.png)

### Automation of VPC traffic Mirroring

![Automation of traffic Mirroring](./mirroring_automation.png)

- We can enable traffic mirroring only when an suspicious activity is identified in the Event Bridge alert

### Centralized Deployment Model

- Note: higher data transfer costs because of *Transit Gateway*

![Centralized Deployment Model](./mirroring_centralized_deployment.png)