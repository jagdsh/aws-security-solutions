# CloudFormation

- CloudFormation is a declarative way of outlining your AWS Infrastructure, for any resources (most of them are supported).
- For example, within a CloudFormation template, you say:
  - I want a security group
  - I want two EC2 instances using this security group
  - I want an S3 bucket
  - I want a load balancer (ELB) in front of these machines
- Then CloudFormation creates those for you, in the right order, with the exact configuration that you specify

## Glossary

| Component | Description |
| --------- | --------- |
| Templates | The JSON or YAML text file that contains the instructions for building out the AWS environment |
| Stacks | The entire environment described by the template and created, updated, and deleted as a single unit |
| StackSets | AWS CloudFormation StackSets extends the functionality of stacks by enabling you to create, update, or delete stacks across multiple accounts and regions with a single operation |
| Change Sets | A summary of proposed changes to your stack that will allow you to see how those changes might impact your existing resources before implementing them

## Benefits of AWS CloudFormation

- Infrastructure as code
  - No resources are manually created, which is excellent for control
  - Changes to the infrastructure are reviewed through code
- Cost
  - Each resources within the stack is tagged with an identifier so you can easily see how much a stack costs you
  - You can estimate the costs of your resources using the CloudFormation template
  - Savings strategy: In Dev, you could automation deletion of templates at 5 PM and recreated at 8 AM, safely
- Productivity
  - Ability to destroy and re-create an infrastructure on the cloud on the fly
  - Automated generation of Diagram for your templates!
  - Declarative programming (no need to figure out ordering and orchestration)
- Don’t re-invent the wheel
  - Leverage existing templates on the web!
  - Leverage the documentation
- Supports (almost) all AWS resources:
  - Everything we’ll see in this course is supported
  - You can use "custom resources" for resources that are not supported
- The template can reused for multiple regions, accounts with Infrastructure as Code (IaC).

## CloudFormation Stack Designer

- Example: WordPress CloudFormation Stack
- We can see all the resources
- We can see the relations between the components

View Designer:
![Stack Designer](./stack_designer.png)

- Estimate cost
- Change set preview
- Status
  - CREATE_IN_PROGRESS
  - CREATE_IN_PROGRESS
  - CREATE_COMPELETE
  - UPDATE_COMPELETE

## CloudFormation Drift

- CloudFormation allows you to create infrastructure
- But it doesn’t protect you against manual configuration changes
- How do we know if our resources have drifted?
- We can use CloudFormation drift!
- Not all resources are supported yet: [Drift](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-drift-resource-list.html)

## Termination Protection on Stacks

- To prevent accidental deletes of CloudFormation templates, use TerminationProtection

## Stack Policies

- Stack policies can help prevent stack resources from being unintentionally updated or deleted during a stack update. A stack policy is a JSON document that defines the update actions that can be performed on designated resources.

- By default, any IAM principal with `cloudformation:UpdateStack` permissions can update all of the resources in an AWS CloudFormation stack.

- Updates can cause interruptions, or they can completely delete and replace resources. You can use a stack policy to help configure least-privilege permissions.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["cloudformation:SetStackPolicy"],
      "Resource": "*",
      "Condition": {
        "StringLike": {
          "cloudformation:StackPolicyUrl": "<Bucket URL>/<Team folder>/*"
        }
      }
    }
  ]
}
```

## Dynamic References

- You can reference external values stored in SSM Parameter Store or Secrets Manager within a CFN template
- CloudFormation retrieves the value during stack create & update operations
- Database credentials, passwords, 3rd party API keys, etc…
- Example: retrieve RDS DB Instance master password from Secrets Manager
- Supports

  - ssm – plaintext values stored in SSM Parameter Store
  - ssm-secure – secure strings stored in SSM Parameter Store
  - secretsmanager –secret values stored in Secrets Manager

  `{{resolve:service-name:reference-key}}`

  ![Dynamic Reference](./dynamic_reference.png)

  ![Dynamic Reference 2](./dynamic_reference_2.png)

````yaml
Resources:
  MyIAMUser:
    Type: AWS::IAM::User
    Properties:
      UserName: stephane
      LoginProfile:
      ```
      Password: '{{resolve:ssm-secure:IAMUserPassword}}'
      ```
````

````yaml
Resources:
  MyRDSDBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      DBName: MyRDSDBInstance
    ```
      MasterUsername: '{{resolve: secretsmanager:MyRDSSecret:SecretString:username}}' MasterUserPassword: '{{resolve:secretsmanager:MyRDSSecret:SecretString:password}}'
    ```
````

![Dynamic Reference 2](./dynamic_reference_3.png)

## CloudFormation Guard (cfn-guard)

- An open-source CLI tool to validate CFN templates against organization policy guidelines
- Example: ensure users always create encrypted S3 buckets
- You define your own policies as code using a declarative Domain-Specific Language (DSL)
- Provides a built-in testing framework to verify that your rules work as intended
- Doesn’t validate Cloud Formation (CFN) templates against syntax
- Can be used as part of CI/CD pipeline

`cfn-guard validate -d template.yaml -r rules.guard --show-summary-pass`

````json
AWS::S3::Bucket {
  ```
Properties {
  BucketEncryption.ServerSideEncryptionConfiguration [*] {
  ServerSideEncryptionByDefault.SSEAlgorithm == 'AES256'|
  «BucketEncryption not configured with the AES256 algorithm»>
}
```
}
}
````

rules.guard

```yaml
Properties:
  BucketEncryption:
    ServerSideEncryptionConfiguration:
      - ServerSideEncryptionByDefault:
          SSEAlgorithm: AES256
```

template.yaml

## AWS Service Catalog

- Users that are new to AWS have too many options, and may create
  stacks that are not compliant / in line with the rest of the organization
- Some users just want a quick self-service portal to launch a set of authorized products pre-defined by admins
- Includes: virtual machines, databases, storage options, etc…
- Enter AWS Service Catalog!

![Service Catalog](./service_catalog_diagram.png)

## AWS Resource Access Manager (RAM)

- Share AWS resources that you own with other AWS accounts
- Share with any account or within your Organization
- Avoid resource duplication!
- VPC Subnets:
  - allow to have all the resources launched in the same subnets
  - must be from the same AWS Organizations.
  - Cannot share security groups and default VPC
  - Participants can manage their own resources in there
  - Participants can't view, modify, delete resources that belong to other participants or the owner
- AWS Transit Gateway
- Route53 Resolver Rules
- License Manager Configurations

### Sharing VPCs with AWS Organizations and RAM

- VPCs sharing allows sharing of subnets with other accounts
- Uses AWS Resource Access Manager (AWS RAM)
- Integrates with AWS Organizations
- RAM can be used to share many other resources including:
- Transit gateways
- AWS Network Firewalls firewalls • Amazon Route 53 Resolver rules • AWS Cloud WAN

![VPC Sharing](./vpc_sharing.png)

### VPC example

- Each account…
  - is responsible for its own resources
  - cannot view, modify or delete other resources in other accounts
- Network is shared so…
  - Anything deployed in the VPC can talk to other resources in the VPC
  - Applications are accessed easily across accounts, using private IP!
  - Security groups from other accounts can be referenced for maximum security

![Resource Manager VPC](./resource_manager_vpc.png)
