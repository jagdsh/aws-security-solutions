# IAM Security Tools

## IAM Credentials Report

- A report that lists all your account's users and the status of their various credentials

## IAM Access Advisor

- Access advisor shows the service permissions granted to a user and when those services were last accessed.
- You can use this information to revise your policies.

## IAM Access Analyzer

Find out which resources are shared externally
• S3 Buckets
• IAM Roles
• KMS Keys
• Lambda Functions and Layers
• SQS queues
• Secrets Manager Secrets
• Define Zone of Trust = AWS Account or AWS Organization
• Access outside zone of trusts => findings

![Zone of Trust](./iam_references/IAM_access_analyzer.png)

### IAM Access Analyzer Policy Validation

- Validates your policy against IAM policy grammar and best practices
- General warnings, security warnings, errors, suggestions
- Provides actionable recommendations

### IAM Access Analyzer Policy Generation

- Generates IAM policy based on access activity
- CloudTrail logs is reviewed to generate the policy with the fine-grained permissions and the appropriate Actions and Services
- Reviews CloudTrail logs for up to 90 days

![Policy Generator](./iam_references/IAM_Policy_generator.png)

## IAM Policies

### Structure

![Policy Structure](./iam_references/iam_policy_structure.png)

### NotAction with Allow

- Provide access to all the actions in an AWS service, except for the actions specified in NotAction
- Use with the Resource element to provide scope for the policy, limiting the allowed actions to the actions that can be performed on the specified resource

![NotAction with Allow](./iam_references/iam_policy_notaction_with_allow.png)

- It does notmean `iam` is rejected, but not authorized in this statement
- It means allow everything but delete action in S3, but not authorized to delete the the bucket

### NotAction with Deny

- Use the NotAction element in a statement with "Effect": "Deny" to deny access to all the listed resources except for the actions specified in the NotAction element
- This combination does not allow the listed items, but instead explicitly denies the actions not listed
- You must still allow explicitly actions that you want to allow

![NotAction with Deny](./iam_references/iam_policy_notaction_with_deny.png)

- Basically it says if the user is not using MFA then deny authorization for all resources except IAM
- Deny all actions except `IAM` for all resources if the MFA auth is false
- This is because the user needs to access IAM to get MFA token

### Restrict to One Region (NotAction)

![Deny everything outside of eu-central-1](./iam_references/iam_policy_deny_outside_eu.png)

![Deny everything outside of eu-central-1 except Amazon S3](./iam_references/iam_policy_deny_outside_eu_except_s3.png)

### (Action | Not Action) & (Allow | Deny)

![Matrix of Action | Not Action](./iam_references/iam_policy_action_not_action.png)

### Principal Options

- AWS Account and Root UserUser
![AWS Account and Root UserUser](./iam_references/iam_principal_options_account_root.png)

- IAM Roles
![IAM Roles](./iam_references/iam_principal_options_iam_role.png)

- IAM Role Sessions
![IAM Role Sessions](./iam_references/iam_principal_options_iam_role_sessions.png)

- IAM Users
![IAM Users](./iam_references/iam_principal_options_iam_user.png)

- Federated User Sessions
![Federated User Sessions](./iam_references/iam_principal_options_federated_user_session.png)

- AWS Services
![AWS Services](./iam_references/iam_principal_options_aws_services.png)

- All Principals
![All Principals](./iam_references/iam_principal_options_all_principles.png)

## IAM Policies - Condition

### Condition Operators

- StringEquals / StringNotEquals – Case Sensitive, Exact Matching
![StringEquals](./iam_references/iam_conditions_stringequals.png)

- StringLike / StringNotLike – Case Sensitive, optional Partial Matching using *,?
![StringLike](./iam_references/iam_conditions_stringlike.png)

- DateEquals, DateLessThan
![DateGreaterThan](./iam_references/iam_conditions_dategreaterthan.png)

- ArnLike / ArnNotLike – used specifically for ARN

- Bool – check for Boolean value
![Bool](./iam_references/iam_conditions_bool.png)

- IpAddress / NotIpAddress (CIDR format)
  - Resolves to the IP address that the request originates from
  - Public IP only – IpAddress does not apply to requests through VPC Endpoints

![IpAddress](./iam_references/iam_conditions_ipaddress.png)

### RequestedRegion

- The AWS region of the request
- Used to restrict specific actions in specific AWS regions

![Restrict Regions](./iam_references/iam_conditions_requested_region_1.png)

- When using a global AWS service (e.g.,IAM, CloudFront, Route53, Support), the AWS region is always us-east-1
- Work around using NotAction and Deny

![Restrict Regions](./iam_references/iam_conditions_requested_region_2.png)

- Deny everything except IAM, CloudFront, Route53, Support if the request is coming from outside these regions

### PrincipalArn

- Compare the ARN of the principal that made the request with the ARN specified in the policy
- Note: For IAM roles, the request context returns theARN of the role, not the ARN of the user that assumed the role
- The following types of Principals are allowed:
  - IAM role arn:aws:iam::123456789012:role/role-name
  - IAM user arn:aws:iam::123456789012:user/user-name
  - AWS Root User arn:aws:iam::123456789012:root
  - AWS STS federated user session

![PrincipalArn](./iam_references/iam_conditions_PrincipalArn.png)

### SourceArn

- Compare the ARN of the resource making a service-to-service request with the ARN that you specify in the policy
- This key is included in the request context only if accessing a resource triggers an AWS service to call another service on behalf of the resource owner
- Example: an S3 bucket update triggers an SNS topic sns:Publish API, the policy set the value of the condition key to the ARN of the S3 bucket

![SourceArn](./iam_references/iam_conditions_SourceArn.png)

### CalledVia

- Look at the AWS service that made the request on behalf of the IAM User or Role
- Contains an ordered list of each AWS service in the chain that made requests on the principal's behalf
- Supports:
  - athena.amazonaws.com
  - cloudformation.amazonaws.com
  - dynamodb.amazonaws.com
  - kms.amazonaws.com

![CalledVia](./iam_references/iam_conditions_CalledVia.png)

### IP & VPC Conditions

- aws:SourceIp
  - Public requester IP (e.g., public EC2 IP if coming from EC2)
  - Not present in requests through VPC Endpoints
- aws:VpcSourceIp – requester IP through VPC Endpoints (private IP)
- aws:SourceVpce – restrict access to a specific VPC Endpoint
- aws:SourceVpc
  - Restrict to a specific VPC ID
  - Request must be made through a VPC Endpoint
- Common to use these conditions with S3 Bucket Policies

![Restrict Access from Private IP Addresses (through a VPC Endpoint)](./iam_references/iam_conditions_vpc.png)

![Restrict Access from Public IP Addresses](./iam_references/iam_conditions_ip.png)

### ResourceTag & PrincipalTag

- Controls access to AWS Resources using tags
- aws:ResourceTag – tags that exist on AWS Resources
  - Sometimes you will see ec2:ResourceTag (service-specific)
- aws:PrincipalTag – tags that exist on the IAM user or IAM role making the request

![ResourceTag & PrincipalTag](./iam_references/iam_conditions_resource_principal_tag.png)

## IAM Permission Boundaries

- IAM Permission Boundaries are supported for users and roles (not groups)
- Advanced feature to use a managed policy to set the maximum permissions an IAM entity can get.

![Boundaries](./iam_references/iam_permission_boundaries.png)

- Use cases
  - Delegate responsibilities to non administrators within their permission boundaries, for example create new IAM users
  - Allow developers to self-assign policies and manage their own permissions, while making sure they can’t “escalate” their privileges (= make themselves admin)
  - Useful to restrict one specific user (instead of a whole account using Organizations & SCP)

Can be used in combinations of AWS Organizations SCP

![Effective Permissions](./iam_references/iam_effective_permission.png)

[Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html)

## Evaluation Logic

### Simplified Evaluation Logic (Allow/Deny)

1. By default, all requests are implicitly denied except for the AWS account root user, which has full access.
2. An explicit allow in an identity-based or resource-based policy overrides the default in 1)
3. If a permissions boundary, Organizations SCP, or session policy is present, an explicit allow is used to limit actions. Anything not explicitly allowed is an implicit deny and may override the decision in 2)
4. An explicit deny in any policy overrides any allows

### IAM Policy Evaluation Logic

![Evaluation Logic](./iam_references/iam_evaluation_logic.png)

[Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html)

### Example IAM Policy

- Can you perform sqs:CreateQueue?
- Can you perform sqs:DeleteQueue?
- Can you perform ec2:DescribeInstances

![Example IAM Policy](./iam_references/iam_example.png)

### Cross-Account Access Policy Evaluation Logic

![Cross-Account Evaluation Logic](./iam_references/cross_accoun_evaluation_logic.png)

- Request from Account A to Account B:
- The requester in Account A must have an identity-based policy
- That policy must allow the requester to make a request to the resource in Account B
- The resource-based policy in Account B must allow the requester in Account A to access the resource

## IAM Policy combination

### IAM Policy + Resource Policy

![IAM Policy](./iam_resource_iam_policy.png)

![Resource Policy (S3 Bucket Policy)](./iam_resource_bucket_policy.png)

### IAM Policy + VPC Endpoint Policy + Resource Policy

![IAM + VPC + Resource](./iam_vpc_resource.png)

- ![EC2 Instance Profile](./iam_vpc_resource_ec2_policy.png)

- ![VPC Endpoint Policy](./iam_vpc_resource_endpoint_policy.png)

- ![Bucket Policy](./iam_vpc_resource_bucket_policy.png)

### IAM Roles vs Resource Based Policies

- Cross account:
  - attaching a resource-based policy to a resource (example: S3 bucket policy)
  - OR using a role as a proxy

![Cross Account policy](./resource_policy_references/iam_vs_resource_cross_account.png)

- When you assume a role (user, application or service), you give up your original permissions and take the permissions assigned to the role
- When using a resource-based policy, the principal doesn’t have to give up his permissions
- Example: User in account A needs to scan a DynamoDB table in Account A and dump it in an S3 bucket in Account B.
- Supported by: Amazon S3 buckets, SNS topics, SQS queues, etc

- IAM Roles:
  - Helpful to give temporary permissions for a specific task
  - Allow a user/application to perform many actions in a different account
  - Permissions expire over time
- Resource-based policies:
  - Used to control access to specific resources (resource-centric view)
  - Allow cross-account access
  - Permanent authorization (as long as it exists in the resource-based policy)

### Resource Policies & aws:PrincipalOrgID

- aws:PrincipalOrgID can be used in any resource policies to restrictaccess to accounts that are member of an AWS Organization

![Resource Policy PrincipalOrgID Diagram](./resource_policy_references/resource_policy_principalOrgId_diagram.png)

![Resource Policy PrincipalOrgID policy format](./resource_policy_references/resource_policy_principalOrgId.png)
