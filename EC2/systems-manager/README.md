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

[Reference](./ssm_run_command.png)

### SSM Automation