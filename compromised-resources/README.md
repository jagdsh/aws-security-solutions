# What to do when one of your AWS Resources is compromised?

    The main steps involed in any compromised resources
    - Identify the source
    - Isolate the resource using SG by not rejecting egress etc
    - Analyse the compromised resources in deep using logs etc
    - Scope out the blast radius of the issue.

## Compromised EC2 Instance
Steps to address compromised instances:
    - Capture the instance’s metadata
    - Enable Termination Protection
        - Because we want to keep it and doesn't want to disappear
    - Isolate the instance (replace instance’s SG – no outbound traffic authorized)
        - Cannot comunicate the outside world, atleast it can be contained
    - Detach the instance from any ASG (Suspend processes)
    - Deregister the instance from any ELB
        - We don't want that instance part of any network traffic
    - Snapshot the EBS volumes (deep analysis)
        - Since the root cause can be originated from the attached volume, it is better to create snapshot of the volume for analysis
        - We create a forensic instance and we can attach the snapshot to it for deep analysis
    - Tag the EC2 instance (e.g., investigation ticket)

![Entire Flow](./compromised_ec2.png)


### Investigation
- Offline investigation
    - It can be contucted by shutting down the instance and analyzing the logs.
    - investigate the volumes
- Online investigation
    - EC2 instance is still active and we can analyze what information is send to outside world
    - (e.g., snapshot memory of capture network traffic)

### Automation

- We can automate the isolation process using Lambda and step functions
- Automate the memory capture: SSM Run command

## Compromised S3 Bucket

- Identify the compromised S3 bucket using *GuardDuty*
- Identify the source of the malicious activity (e.g., IAM user, role) and the API calls using *CloudTrail* or *Amazon Detective*
- Identify whether the source was authorized to make those API calls
- Secure your S3 bucket, recommended settings:
    - S3 Block Public Access Settings 
    - S3 Bucket Policies and User Policies 
    - VPC Endpoints for S3
    - S3 Pre-signed URLs 
    - S3 Access Points
    - S3 ACLs - Deprecated

![Entire Flow](./compromised_s3.png)

## Compromised ECS Cluster

- Identify the affected ECS Cluster using GuardDuty
    - The Guard duty gives security events if there is any malicious activity going. 
- Identify the source of the malicious activity 
    - Is it container image, tasks or some other resources
- Isolate the impacted tasks 
    - deny all ingress/egress traffic to the task using security groups
- Evaluate the presence of malicious activity 
    - like if there was Malware injected into the container

![Entire Flow](./compromised_ecs.png)

## Compromised Standalone Container

- Identify the malicious container using GuardDuty
- Isolate the malicious container using a new security group
    - deny all ingress/egress traffic to the container
- Suspend all process in the container 
    - pause the container
- Or stop the container and look at EBS Snapshots retained by GuardDuty 
    - snapshots retention feature
- Evaluate the presence of malicious activity 
    - e.g., malware

![Entire Flow](./compromised_standalone_container.png)


## Compromised RDS Instance

- Identify the affected DB instance and DB user using GuardDuty
- If it is NOT legitimate behavior:
    - Restrict network access (SGs & NALCs)
    - Restrict the DB access for the suspected DB user
- Rotate the suspected DB users’ passwords
- Review DB Audit Logs to identify leaked data
- Secure your RDS DB instance, recommended settings:
    - Use Secrets Manager to rotate the DB password
    - Use IAM DB Authentication to manage DB users’ access without passwords

![Entire Flow](./compromised_rds.png)


## Compromised AWS credentials

- Identify the affected IAM user using GuardDuty
- Rotate the exposed AWS Credentials
- Invalidate temporary credentials by *attaching an explicit Deny policy* to the affected IAM user with an STS date condition
- Check CloudTrail logs for other unauthorized activity
- Review your AWS resources
    - delete unauthorized resources
- Verify your AWS account information because it could have been changed

![Entire Flow](./compromised_aws_creds.png)

## Compromised IAM role 

- Identify the IAMrole
- Invalidate temporary credentials by attaching an explicit Deny policy to the affected IAM user with an STS date condition
- Revoke access for the identity to the linked AD if any 
    - Like SSO
- Check CloudTrail logs for other unauthorized activity
- Review your AWS resources (e.g., delete unauthorized resources)
- Verify your AWS account information

![Entire Flow](./compromised_iam_role.png)

## Compromised AWS Account

- Rotate and delete exposed AWS Access Keys
- Rotate and delete any unauthorized IAM user credentials
    - rotate existing IAM users’ passwords
- Rotate and delete all EC2 Key Pairs
- Check CloudTrail logs for other unauthorized activity
- Review your AWS resources 
    - delete unauthorized resources
- Verify your AWS account information

![Entire Flow](./compromised_account.png)

