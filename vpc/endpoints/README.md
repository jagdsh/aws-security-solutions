# VPC Endpoints

- Endpoints allow you to connect to AWS Services using a private network instead of the public www network
- They scale horizontally and are redundant
- No more IGW, NAT, etc… to access AWS Services
- VPC Endpoint Gateway (S3 & DynamoDB)
- VPC Endpoint Interface (all except DynamoDB)
- In case of issues:
  - Check DNS Setting Resolution in your VPC
  - Check Route Tables

![VPC Endpoint](./vpc_endpoint.png)

## VPC Endpoint Gateway

- Only works for S3 and DynamoDB, must create one gateway per VPC
- Must update route tables entries (no security groups!)
- Gateway is defined at the VPC level
- DNS resolution must be enabled in the VPC
- The same public hostname for S3 can be used
- Gateway endpoint cannot be extended out of a VPC (VPN, DX, TGW, peering)

![VPC Endpoint Gateway](./vpc-endpoint_gateway.png)

## VPC Endpoints Interface (VPC ENI)

- Provision an ENI that will have a private endpoint interface hostname
- Leverage Security Groups for security
- Private DNS (setting when you create the endpoint)
  - The public hostname of a service will resolve to the private Endpoint Interface hostname
  - VPC Setting: “Enable DNS hostnames” and “Enable DNS Support” must be 'true’
  - Example for Athena:
    - vpce-0b7d2995e9dfe5418-mwrths3x.athena.us-east-1.vpce.amazonaws.com
    - vpce-0b7d2995e9dfe5418-mwrths3x-us-east-1a.athena.us-east-1.vpce.amazonaws.com
    - vpce-0b7d2995e9dfe5418-mwrths3x-us-east-1b.athena.us-east-1.vpce.amazonaws.com
    - athena.us-east-1.amazonaws.com (private DNS name)
- Interface can be accessed from Direct Connect and Site-to-Site VPN

![VPC Endpoint Interface](./vpc_endpoint_eni.png)

- The traffice will be routed through VPC ENI to reach the resources

## VPC Endpoint Policy

- Controls which AWS principals (AWS accounts, IAM users, IAM Roles) can use the VPC Endpoint to access AWS services
- Can be attached to both Interface Endpoint and Gateway Endpoint
- Can restrict specific API calls on specific resources
- Doesn’t override or replace Identity-based Policies or service-specific policies (e.g., S3 bucket policy)
- Note: can use aws:PrincipalOrgId to restrict access only within the Organization

![VPC Endpoint Policy Format](./vpc_endpoint_policy.png)

![VPC Endpoint Policy Diagram](./vpc_endpoint_policy_diagram.png)

## VPC Endpoint Policy – Authorization logic

- VPC Endpoint Policy + Identity-based Policy

![VPC endpoint Policy & Identity](./vpc_endpoint_policy_identity_authorization_logic.png)

- VPC Endpoint Policy + Resource-based Policy + IAM Role

![VPC endpoint Policy & Resource & Identity](./vpc_endpoint_policy_resource_identity_authorization_logic.png)

## VPC Endpoint Examples

### VPC Endpoint – CodeDeploy

![Code Deploy](./vpc_endpoint_eg_code_deploy.png)

### VPC Endpoint – Secrets Manager

![Accessing Secrets Manager](./vpc_endpoint_eg_secrets_manager.png)

### VPC Endpoint – SSM Session Manager

- Connect to EC2 instances in private subnets, without Internet access

![Session Manager](./vpc_endpoint_eg_session_manager.png)

### VPC Endpoint – Patch Manager

- Note: VPC endpoint policy must allow access to those S3 buckets:
- arn:aws:s3:::patch-baseline-snapshot-region/*
- arn:aws:s3:::aws-ssm-region/*
- Any other s3 buckets required by SSM
- Note: for S3 Gateway Endpoint, update route tables

![Patch Manager](./vpc_endpoint_eg_patch_manager.png)

### VPC Endpoint – API Gateway

- Private REST APIs can only be accessed using VPC Interface Endpoint
- VPC Endpoint Policies can be used together with API Gateway resource policies
- Restrict access to your private APIs from VPC and VPC Endpoints using resource policies (aws:SourceVpc and aws:SourceVpce)

![API Gateway](./vpc_endpoint_eg_api_gateway.png)

![API Gateway Resource poolicy](./vpc_endpoint_eg_api_gateway_resource_policy.png)

## Exposing services in your VPC to other VPC

### Option 1: Make it public

- Goes through the public www
- Tough to manage access

![Option 1](./exposing_vpc_to_vpc_option_1.png)

### Option 2: VPC Peering

- Must create many peering relations
- Opens the whole network

![Optiion 2](./exposing_vpc_to_vpc_option_2.png)
