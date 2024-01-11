# Penetration Testing

We can run penetration testing on our AWS account from external services without approval for 8 services:
    - Amazon EC2 instances, NAT Gateways, and Elastic Load Balancers
    - Amazon RDS
    - Amazon CloudFront
    - Amazon Aurora
    - Amazon API Gateways
    - AWS Lambda and Lambda Edge functions
    - Amazon Lightsail resources
    - Amazon Elastic Beanstalk environments

Replicating following Activities are prohibited:
    - DNS zone walking via Amazon Route 53 Hosted Zones
    - Denial of Service (DoS), Distributed Denial of Service (DDoS), Simulated DoS and DDoS
    - Port flooding
    - Protocol flooding
    - Request flooding (login request flooding, API request flooding)


# DDoS Simulation testing on AWS

- Controlled DDoS attack which enables you to evaluate the resiliency of your applications and to practice event response
- Must be performed by an approved *AWS DDoS Test Partner*
- The target can be either *Protected Resources* or *Edge-Optimized API Gateway* that is subscribed to *Shield Advanced*
- Attack must NOT be originated from AWS resources
- Attack must NOT exceed 20 Gigabits/second
- Attack must NOT exceed 5 million packets/second for CloudFront and 50,000 packets/second for any other service

