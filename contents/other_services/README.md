# Other Services

## Amazon WorkSpaces

### Security IP Access Control Groups

- Similar to Security Groups but for WorkSpaces
- List of IP addresses / CIDR address ranges that users are authorized to connect from
- If users access WorkSpaces through VPN or NAT, the IP Access Control Group must authorize the public IP of these

![Workspaces Access Control](./workspaces_ip_access_control.png)

### Security Access Control Options and Trusted Devices

- Manage which client devices can access WorkSpaces
- WorkSpaces Certificate-based Authentication
  - Limit access only to trusted devices using
    Digital Certificates
  - Only Windows, MacOS, and Android clients

![Workspaces Trusted Devices](./worksapces_trusted_devices.png)

## Auto Scaling

### Instance Refresh

- Goal: update launch template and then re-creating all EC2 instances
- For this we can use the native feature of Instance Refresh
- Setting of minimum healthy percentage
- Specify warm-up time (how long until the instance is ready to use)

![Auto Scaling Instance Refresh](./auto_scaling_instance_refresh.png)

## EBS Volumes – Data Wiping

- When you delete an EBS volume.
- The physical block storage used by deleted EBS volumes is overwritten with zeroes before it is allocated to a new volume
- Note: no need to manually wipe data

![EBS Volume Wiping](./ebs_volume_swiping.png)

## AWS CloudShell

- Browser-based shell to manage your AWS resources
- Run AWS CLI commands & run scripts without leaving your browser
- Pre-installed common libraries and command-line tools:
  - AWS CLI, ECS CLI, SAM CLI, and AWS SDKs for Python and Node.js
  - Bash, Powershell, zsh, vi (editor), git, npm, pip, …
- Pre-configured AWS Credentials (inherit from the logged in user)
- Persistent storage with up to 1GB per AWS region
- Runs on Amazon Linux 2
- Can’t access resources in a VPC (EC2, RDS, …)
  - for EC2 in VPC should use EC2 connect for connection

![Cloudshell](./cloud_shell.png)

## RDS & Aurora Security

- At-rest encryption:
  - Database master & replicas encryption using AWS KMS – must be defined as launch time
  - If the master is not encrypted, the read replicas cannot be encrypted
  - To encrypt an un-encrypted database, go through a DB snapshot & restore as encrypted
- In-flight encryption: TLS-ready by default, use the AWS TLS root certificates client-side
- IAM Authentication: IAM roles to connect to your database (instead of username/pw)
- Security Groups: Control Network access to your RDS / Aurora DB
- No SSH available except on RDS Custom
- Audit Logs can be enabled and sent to CloudWatch Logs for longer retention

### RDS - IAM Authentication

- IAM database authentication works with MariaDB, MySQL and PostgreSQL
- You don’t need a password, just an authentication token obtained through IAM & RDS API calls
- Auth token has a lifetime of 15 minutes
- Benefits:
  - Network in/out must be encrypted using SSL
  - IAM to centrally manage users instead of DB
  - Can leverage IAM Roles and EC2 Instance profiles for easy integration

![RDS IAM Authentication](./iam_authentication.png)

### Aurora – Encrypt un-encrypted Snapshot

- When you encrypt Aurora cluster then all DB instances, snapshots, logs, and backups are all encrypted
- You can’t create an encrypted snapshot of an un-encrypted DB cluster
- Note: you can’t encrypt an un-encrypted snapshot while you copy the snapshot

![Aurora Snapshot](./aurora_snapshot.png)

## EC2 Image Builder

- Used to automate the creation of Virtual Machines or container images
- => Automate the creation, maintain, validate and test EC2 AMIs
- Can be run on a schedule (weekly, whenever packages are updated, etc…)
- Free service (only pay for the underlying resources)

![EC2 Image Builder](./ec2_image_builder.png)

### Troubleshooting

- Error : “AccessDenied: Access Denied status code: 403”
- Reasons:
  - Instance profile Role doesn’t have the required permissions
  - Instance Profile Role is missing permissions required for logging to Amazon S3
- Resolution:
  - Make sure the following Managed Policies added to your Instance Profile Role: - AmazonSSMManagedInstanceCore,
    EC2InstanceProfileForImageBuilder,
    EC2InstanceProfileForImageBuilderECRContainerBuilds
  - Add a Policy to your Instance Profile Role that grants PutObject
    permissions to write to your S3 Bucket

![EC2 Image builder](./ec2_image_builder_troubleshooting.png)

## Amazon Redshift

### Database Hierarchy

- Superusers
  - Have the same permissions as DB owners for all databases
  - Example: admin user which is created when you launch the Redshift Cluster
  - You must be a superuser to create a superuser
- Users
  - Can only be created and dropped by a superuser
  - Can own databases and database objects (e.g., tables)
  - Can grant permissions on those objects to other DB Users, Groups, and Schemas
  - User is granted permissions in two ways
    - Explicitly, by having those permissions assigned directly to the account
    - Implicitly, by being a member of a group that is granted permissions
- Groups
  - Collections of Users that can be collectively assigned permissions
  - Good for streamlined security maintenance
- Database
  - Collection of one or more Schema
  - When a User create a database, the User becomes its owner
  - Superusers have the same permissions as database owners
- Schema
  - Collections of database tables and other database objects
  - Used to group database objects under a common name
  - Users can be granted access to a single schema or to multiple schemas

![Redshift Hierarchy](./redshift_database_hierarchy.png)

### In-DB Security

- Commonly, Users log in to database by providing a database username and password
- However, you don't have to maintain usernames and passwords in your Amazon Redshift database
  - You can configure your system to permit users to create database credentials and log in
    to the database based on their IAM credentials
  - Redshift provides the GetClusterCredentials API operation to generate temporary database user credentials

![Redshift Security](./redshift_security.png)

**existing database user:**

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "redshift:GetClusterCredentials",
    "Resource": "arn:aws:redshift:us-west-2:123456789012:dbuser: examplecluster/temp_creds_user"
  }
}
```

- When you create temporary user credentials for an existing database user, you can disable the user's password to force the user to log on with the temporary password
- Alternatively, you can use the GetClusterCredentials Autocreate option to automatically create a new database user each time you connect
- To create a read-only user, add a user to a group that only has read-only privileges to the specified Schemas for a database
  - Note that you will still have to initially manually specify all of the schema names & then subsequently modify the group for any new schemas that you may create

## DynamoDB – Time To Live (TTL)

- Automatically delete items after an expiry timestamp (no extra cost)
- The TTL attribute must be a “Number” data type with “Unix Epoch timestamp” value
- Expired items deleted within 48 hours of expiration
- Use cases: reduce stored data by keeping only current items, adhere to regulatory obligations

![DynamoDB TTL](./dynamodb_ttl.png)
