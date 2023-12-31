# Load Balancing

- Load balancers are servers that forward internet traffic to multiple servers (EC2 Instances) downstream.

![Load Balancing](./load_balancing.png)

## Why use a load balancer?

- Spread load across multiple downstream instances
- Expose a single point of access (DNS) to your application
- Seamlessly handle failures of downstream instances
- Do regular health checks to your instances
- Provide SSL termination (HTTPS) for your websites
- High availability across zones

## Why use an Elastic Load Balancer?

- An ELB (Elastic Load Balancer) is a managed load balancer
  - AWS guarantees that it will be working
  - AWS takes care of upgrades, maintenance, high availability
  - AWS provides only a few configuration knobs
- It costs less to setup your own load balancer but it will be a lot more effort on your end (maintenance, integrations)
- 4 kinds of load balancers offered by AWS:
  - Application Load Balancer (HTTP / HTTPS only) – Layer 7
  - Network Load Balancer (ultra-high performance, allows for TCP) – Layer 4
  - Gateway Load Balancer – Layer 3
  - Classic Load Balancer (retired in 2023) – Layer 4 & 7

### Application Load Balancer

- HTTP / HTTPS / gRPC protocols (Layer 7)
• HTTP Routing features
• Static DNS (URL)

![Application Load Balancer](./lb_alb.png)

### Network Load Balancer

- TCP / UDP protocols (Layer 4)
- High Performance: millions of request per seconds
- Static IP through Elastic IP

![Network Load Balancer](./lb_nlb.png)

### Gateway Load Balancer

- GENEVE Protocol on IP Packets (Layer 3)
- Route Traffic to Firewalls that you manage on EC2 Instances
- Intrusion detection

![Gateway Load Balancer](./lb_gwlb.png)

## Network Load Balancer (v2)

- Network load balancers (Layer 4) allow to:
  - Forward TCP & UDP traffic to your instances
  - Handle millions of request per seconds
  - Less latency ~100 ms (vs 400 ms for ALB)
- NLB has one static IP per AZ, and supports assigning Elastic IP (helpful for whitelisting specific IP)
- NLB are used for extreme performance, TCP or UDP traffic
- Not included in the AWS free tier

### TCP (Layer 4) Based Traffic

![Network Load Balancer TCP Traffic](./lb_nlb_tcp_traffic.png)

### Target Groups

- EC2 instances
- IP Addresses – must be private IPs
- Application Load Balancer
- Health Checks support the TCP, HTTP and HTTPS Protocols

![Target Groups](./nlb_target_groups.png)

### Client IP Preservation

- Client IP addresses are forwarded to Network Load Balancer targets
- When disabled, the private IP address of the Network Load Balancer becomes the client IP address for all incoming traffic
- Default Settings:
  - Targets by instance ID / ECS Tasks: Enabled
  - Targets by IP address TCP & TLS: Disabled (default)
  - Targets by IP address UDP & TCP_UDP: Enabled (default)
- Note: Can’t be disabled for both targets by instance
ID and by IP address (UDP & TCP_UDP)

![Network Client IP](./nlb_client_ip.png)

### Security Groups

- Network Load Balancers can have associated Security Groups
- Target’s Security Groups can use the NLB Security Group to allow traffic

![Security Groups](./security_groups.png)

### Network ACL

![Network ACL](./nlb_acl.png)

**NLB Subnet NACLs**:

${\color{purple}Inbound:}$
| Source | Protocol | Port Range | Comment |
| ------ | ------ | ------ | ------ |
| Client IP / VPC CIDR | Listener | Listener | Allow traffic from Client and Internet|
| VPC CIDR | Health Check | 1024 – 65535 | Allow Health Check traffic |

${\color{orange}Outbound:}$

| Source | Protocol | Port Range | Comment |
| ------ | ------ | ------ | ------ |
| Client IP / VPC CIDR | Listener | Listener | Allow responses to Client and Internet |
| VPC CIDR | Health Check | Health Check | Allow Health Check traffic |
| VPC CIDR | Health Check | 1024 – 65535 | Allow Health Check traffic |

**Targets Subnet NACLs**:

${\color{purple}Inbound:}$
| Source | Protocol | Port Range | Comment |
| ------ | ------ | ------ | ------ |
| Client IP / VPC CIDR | Listener | Listener | Allow traffic from Client and Internet|
| VPC CIDR | Health Check | Health Check | Allow Health Check traffic from NLB |

${\color{orange}Outbound:}$
| Source | Protocol | Port Range | Comment |
| ------ | ------ | ------ | ------ |
| Client IP / VPC CIDR | Listener | Listener | Allow responses to Client and Internet |
| VPC CIDR | Health Check | Health Check | Allow responses to Client and Internet |
| VPC CIDR | Health Check | 1024 – 65535 | Allow Health Check traffic |

### **[Troubleshooting]**

- Registered Target is NOT In Service
  - Health check can be failing
  - Target’s Security Group does not allow traffic on health check port/protocol from the NLB’s Security Group
  - Target’s Subnet NACL does not allow traffic from the NLB
- Requests are NOT Routed To Targets
  - Target’s Security Group does not allow traffic from the NLB’s Security Group
  - Target’s Subnet NACL does not allow traffic (see before)
  - Targets are in an Availability Zone that is not enabled
  - EC2 instances is in a peered VPC
  - Then you must register EC2 instances by IP addresses, not by Instance ID

## Sticky Sessions (Session Affinity)

- It is possible to implement stickiness so that the same client is always redirected to the same instance behind a load balancer
- This works for Classic Load Balancer, Application Load Balancer, and Network Load Balancer
- For both CLB & ALB, the “cookie” used for stickiness has an expiration date you control
- Use case: make sure the user doesn’t lose his session data
- Enabling stickiness may bring imbalance to the load over the backend EC2 instances

![Sticky Sessions](./sticky_sessions.png)

### Cookie Names

- Application-based Cookies
  - Custom cookie
    - Generated by the target
    - Can include any custom attributes required by the application
    - Cookie name must be specified individually for each target group
    - Don’t use AWSALB, AWSALBAPP, or AWSALBTG (reserved for use by the ELB)
- Application cookie
  - Generated by the load balancer
  - Cookie name is AWSALBAPP
- Duration-based Cookies
  - Cookie generated by the load balancer
  - Cookie name is AWSALB for ALB, AWSELB for CLB

## SSL/TLS - Basics

- An SSL Certificate allows traffic between your clients and your load balancer to be encrypted in transit (in-flight encryption)
- SSL refers to Secure Socket Layer, used to encrypt connections
- TLS refers to Transport Layer Security, which is a newer version
- Nowadays, TLS certificates are mainly used, but people still refer as SSL
- Public SSL certificates are issued by Certificate Authorities (CA)
- Comodo, Symantec, GoDaddy, GlobalSign, DigiCert, Let’s Encrypt, …
- SSL certificates have an expiration date (you set) and must be renewed

## Elastic Load Balancer

### SSL Certificates

- The load balancer uses an X.509 certificate (SSL/TLS server certificate)
- You can manage certificates using ACM (AWS Certificate Manager)
- You can create upload your own certificates alternatively
- HTTPS listener:
  - You must specify a default certificate
  - You can add an optional list of certs to support multiple domains
  - Clients can use SNI (Server Name Indication) to specify the hostname they reach
  - Ability to specify a Security Policy (for compliance, features, compatibility or security)

- Classic Load Balancer
  - Supports only one SSL certificate
  - The SSL certificate can have many Subject Alternate Name (SAN), but the SSL certificate must be changed anytime a SAN is added / edited / removed
  - Must use multiple CLB for multiple hostnames with multiple SSL certificates
  - Better to use ALB with Server Name Indication (SNI) if possible
- Application Load Balancer
  - Supports multiple listeners with multiple SSL certificates
  - Uses Server Name Indication (SNI) to make it work
- Network Load Balancer
  - Supports multiple listeners with multiple SSL certificates
  - Uses Server Name Indication (SNI) to make it work

![ELB SSL Certificates](./elb_ssl_certificates.png)

### SSL – Server Name Indication (SNI)

- SNI solves the problem of loading multiple SSL certificates onto one web server (to serve multiple websites)
- It’s a “newer” protocol, and requires the client to indicate the hostname of the target hostname in the initial SSL handshake
- The server will then find the correct certificate, or return the default one
- Only works for ALB & NLB

![SSL SNI](./ssl_sni.png)

### HTTPS/SSL Listener – Security Policy

- A combination of SSL protocols, SSL ciphers, and Server Order Preference
option supported during SSL negotiations
- Predefined Security Policies (e.g., ELBSecurityPolicy-2016-08)
- For ALB and NLB
  - Frontend connections, you can use a predefined Security Policy
  - Backend connections, ELBSecurityPolicy-2016-08 Security Policy is always used
- Use ELBSecurityPolicy-TLS policies
  - To meet compliance and security standards that require certain TLS protocol version
  - To support older versions of SSL/TLS (legacy clients)
- Use ELBSecurityPolicy-FS policies, if you require Forward Secrecy
  - Provides additional safeguards against the eavesdropping of encrypted data
  - Using a unique random session key

### NLB - SSL/TLS & HTTPS

- NLB uses a Server Certificate to terminate and decrypt the front-end connections before sending them to the targets
- To create a TLS listener, you must attach at least one Server Certificate from ACM on NLB
- If Client IP Preservation Enabled: both source IP address and port is presented to your backend servers, even when TLS is terminated at the NLB
- Network Load Balancers do not support TLS re-negotiation or mutual TLS authentication (mTLS)

![NLB SSL TLS](./nlb_ssl_tls_https.png)

- Use TCP Listener on port 443 to pass encrypted traffic to the targets without the NLB decrypting it
- Important: don’t use a TLS Listener
- The targets must be able to decrypt the traffic
- TCP Listeners also support mTLS, because the NLB passes the request as is, and you can implement mTLS on the targets

![NLB SSL TLS Termination](./nlb_ssl_tls_https_1.png)
