## Systems Manager

- Helps you manage fleet of EC2 and On-Premises systems at scale
- Get operational insights about the state of your infrastructure
- Easily detect problems
- Patching automation for enhanced compliance
- Works for both Windows and Linux OS
- Integrated with CloudWatch metrics / dashboards
- Integrated with AWS Config
- Free service, pay only for the resources, that is used


### AWS Systems Manager Features

- ${\color{yellow}Resource Groups}$
- Operations Management
    - Explorer
    - OpsCenter
    - CloudWatch Dashboard
    - PHD
    - ${\color{yellow}Incident Manager}$
- Shared Resources
    - ${\color{yellow}Documents}$
- Change Management
    - Change Manager
    - ${\color{yellow}Automation}$
    - Change Calendar
    - ${\color{yellow}Maintenance Windows}$
- Application Management
    - Application Manager
    - AppConfig
    - ${\color{yellow}Parameter Store}$
- Node Management
    - Fleet Manager
    - ${\color{yellow}Compliance}$
    - ${\color{yellow}Inventory}$
    - Hybrid Activations
    - ${\color{yellow}Session Manager}$
    - ${\color{yellow}Run Command}$
    - ${\color{yellow}State Manager}$
    - ${\color{yellow}Patch Manager}$
    - Distributer


### How Systems Manager works
![Reference Workflow](./systems-manager-workflow.png)

- We need to install the SSM agent onto the systems we control
- Installed by default on Amazon Linux 2 AMI & some Ubuntu AMI
- There is no need to enable the port, https access for the systems managet to work. 
- The SSM agent in the EC2 machine takes care of communicating and bringing up the instance

## Trobuleshooting

- If an instance can’t be controlled with SSM, it’s probably an issue with the SSM agent.
- Make sure the EC2 instances have a proper IAM Instance profile role to allow SSM actions.
  - The policy name is *AmazonSSMManagedInstanceCore*


## AWS Tags

- You can add text key-value pairs called Tags to many AWS resources
- Commonly used in EC2
- Free naming, common tags are Name, Environment, Team …
- They’re used for
    - Resource grouping
    - Automation
    - Cost allocation

## Resource Groups

![Reference](./resource_group.png)

- Create, view or manage logical group of resources using *AWS tags*
- Allows creation of logical groups of resources such as
    - Applications
    - Different layers of an application stack
    - Production versus development environments
- Regional service
- Works with EC2, S3, DynamoDB, Lambda, etc…
- You can enter Group Details with Name and description


## SSM

- No need for SSH
- No need for enabling inbound / outbound rules of the instance

### Documents

- Documents can be in JSON or YAML
- You define parameters
- You define actions
- Many documents already exist in AWS

![Workflow](./ssm_document_workflow.png)

![Workflow](./ssm_document.png)

### Run Command

- Execute a document (= script) or just run a command
- We can give parameters for the document.
- We can run the command from CLI as well.
- Run command across multiple instances (using resource groups)
- Rate Control / Error Control
- Integrated with IAM & CloudTrail
- Command Output can be shown in the Console, sent to S3 bucket or CloudWatch Logs
- Send notifications to SNS about command statues (In progress, Success, Failed, …)
- Can be invoked using EventBridge

[Example Code](../../sanple_codes/ssm/document-install-apache.yml)

![Reference](./ssm_run_command.png)

### SSM Automation

![Automation](./ssm_automation.png)

- Simplifies common maintenance and deployment tasks of EC2 instances and other AWS resources
- Example: restart instances, create an AMI, EBS snapshot
- Automation Runbook
  - SSM Documents of type Automation
  - Defines actions preformed on your EC2 instances or AWS resources
  - Pre-defined runbooks (AWS) or create custom runbooks
- Can be triggered
  - Manually using AWS Console, AWS CLI or SDK
  - By Amazon EventBridge
  - On a schedule using Maintenance Windows
  - By AWS Config for rules remediations

### SSM Parameter

![SSM Paramter](./ssm_paramter.png)

- Secure storage for configuration and secrets
- Optional Seamless Encryption using KMS
- Serverless, scalable, durable, easy SDK
- Version tracking of configurations / secrets
- Security through IAM
- Notifications with Amazon EventBridge
- Integration with CloudFormation

### SSM Parameter Store Hierarchy

![SSM Parameter](./ssm_getparameter.png)

- ${\color{yellow}/aws/reference/secretsmanager/secret_ID_in_Secrets_Manager}$
- ${\color{yellow}/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2(public)}$
  - This public parameter gives the latest AMI for linux machine

### SSM Parameter policy (For Advanced Parameter)

![SSM Policy](./ssm_policy.png)

- Allow to assign a TTL to a parameter (expiration date) to force updating or deleting sensitive data such as passwords
- Can assign multiple policies at a time

### SSM - Inventory

- Collect metadata from your managed instances (EC2/On-premises)
- Metadata includes installed software, OS drivers, configurations, installed updates, running services
- View data in AWS Console or store in S3 and query and analyze using Athena and QuickSight
- Specify metadata collection interval (minutes, hours, days)
- Query data from multiple AWS accounts and regions
- Create Custom Inventory for your custom metadata (e.g., rack location of each managed instance)

### SSM - State Manager

- Automate the process of keeping your managed instances (EC2/On-premises) in a state that you define
- Use cases: bootstrap instances with software, patch OS/software updates on a schedule
- State Manager Association:
- Defines the state that you want to maintain to your managed instances
- Example: port 22 must be closed, antivirus must be installed …
- Specify a schedule when this configuration is applied
- Uses SSM Documents to create an Association (e.g., SSM Document to configure CW Agent)

### SSM - Patch Manager

![Patch Manager](./ssm_patch_manager.png)

- Automates the process of patching managed instances
- OS updates, applications updates, security updates
- Supports both EC2 instances and on-premises servers
- Supports Linux, macOS, and Windows
- Patch on-demand or on a schedule using Maintenance Windows
- Scan instances and generate patch compliance report (missing patches)
- Patch compliance report can be sent to S3


- Lifecycle hooks?

#### SSM - Patch Manager - Patch Baseline (Per Instance)

- Defines which patches should and shouldn’t be installed on your instances
- Ability to create custom Patch Baselines (specify approved/rejected patches)
- Patches can be auto-approved within days of their release
- By default, install only critical patches and patches related to security

#### Pre-Defined Patch Baseline

    - Managed by AWS for different Operating Systems (can’t be modified)
    - AWS-RunPatchBaseline (SSM Document) – apply both operating system and application patches (Linux, macOS, Windows Server)

#### Custom Patch Baseline

    - Create your own Patch Baseline and choose which patches to auto-approve
    - Operating System, allowed patches, rejected patches, …
    - Ability to specify custom and alternative patch repositories

#### SSM - Patch Manager - Patch Group (Group of Instance)

- Associate a set of instances with a specific Patch Baseline
- Example: create Patch Groups for different environments (dev, test, prod)
- Instances should be defined with the tag key Patch Group
- An instance can only be in one Patch Group
- Patch Group can be registered with only one Patch Baseline

## SSM - Maintenance Windows

- Defines a schedule for when to perform actions on your instances
- Example: OS patching, updating drivers, installing software, …
- Maintenance Window contains
  - Schedule
  - Duration
  - Set of registered instances
  - Set of registered tasks

## SSM – Session Manager

![Session Manager](./ssm_session_manager.png)

- Allows you to start a ${\color{yellow}secure shell}$ on your EC2 and on-premises servers
- Access through AWS Console, AWS CLI, or Session Manager SDK
- ${\color{yellow}Does not need SSH access, bastion hosts, or SSH keys}$
- Supports Linux, macOS, and Windows
- Log connections to your instances and executedcommands
- Session log data can be sent to S3 or CloudWatch Logs
- CloudTrail can intercept StartSession events

- IAM Permissions
  - Control which users/groups can access Session Manager and which instances
  - Use tags to restrict access to only specific EC2 instances
  - Access SSM + write to S3 + write to CloudWatch
- Optionally, you can restrict commands a user can run in a session

- We can specify KMS key for suing a encrypted shell connection
- Can run as specific user
- Enable / Disable Cloudwatch logging.
- It has way more control
