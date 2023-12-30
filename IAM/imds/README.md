# AWS EC2 Instance Metadata Service (IMDS)

- Information about an EC2 instance (e.g., hostname, instance type, network settings, …)
- Can be accessed from within the EC2 instance itself by making a request to the EC2 metadata service endpoint http://169.254.169.254/latest/meta-data
- Can be accessed using EC2 API or CLI tools (e.g., curl or wget)
- Metadata is stored in key-value pairs
- Useful for automating tasks such as setting up an instance's hostname, configuring networking, or installing software

## AWS EC2 Instance Metadata - Example

- ami-id, block-device-mapping/, instance-id, instance-type, network/
- hostname, local-hostname, local-ipv4, public-hostname, public-ipv4
- Iam – InstanceProfileArn, InstanceId
- iam/security-credentials/role-name – temporary credentials for therole attached to your instance
- placement/ – launch Region, launch AZ, placement group name…
- security-groups – names of security groups
- tags/instance – tags attached to the instance

![AWS EC2 Instance Metadata](./ec2_role.png)

## EC2 Instance Metadata – Restrict Access

- You can use local firewall rules to disable access for some or all processes
  - iptables for Linux, PF or IPFW for FreeBSD

  $ sudo iptables --append OUTPUT --proto tcp --destination 169.254.169.254 --match owner --uid-owner apache -jump REJECT

- Turn off access using AWS Console or AWS CLI (HttpEndpoint=disabled)

![Metadata – Restrict Access](./metadata_accessible_disabled.png)

## IMDSv2 vs. IMDSv1

- IMDSv1 is accessing http://169.254.169.254/latest/meta-data directly
- IMDSv2 is more secure and is done in two steps:
  1. Get Session Token (limited validity) – using headers & PUT

```bash
    $TOKEN= curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"'
```

  2. Use Session Token in IMDSv2 calls – using headers

```bash
        curl http://169.254.169.254/latest/meta-data/profile -H "X-aws-ec2-metadata-token: $TOKEN"
```

## Requiring the usage of IMDSv2

- Both IMDSv1 and IMDSv2 are available (enabled by default)
- The CloudWatch Metric MetadataNoToken provide information on how much IMDSv1 is used
- You can force Metadata Version 2 at Instance Launch using either:
- AWS console
- AWS CLI “HttpTokens: required”
- You can require IMDSv2 when registering an AMI: --imds-support v2.0

![IMDSv2 usage](./imdsv2.png)

## Require EC2 Role Credentials to be retrieved from IMDSv2

- AWS credentials provided by the IMDS now include an ec2:RoleDelivery IAM context key
  - 1.0 for IMDSv1
  - 2.0 for IMDS v2
- Attach this policy to the IAM Role of the EC2 Instance
- Or attach it to an S3 bucket to only require IMDSv2 when API calls are made by an IAM role
- Or attach it as an SCP in your account

![EC2 Role Credentials to be retrieved](./ec2_role_credentials_policy.png)

## IAM Policy or SCP to force IMDSv2

- Prevent the launch of an EC2 instance using old instance metadata (IMDSv1)

![SCP to force IMDSv2](./scp_imdsv2_prevent_old_instance_metadata.png)

- Prevent modifying a running EC2 instance using ModifyInstanceMetadataOptions API to re-enable old instance metadata (IMDSv1)

![SCP to force IMDSv2 ModifyInstanceMetadataOptions](./scp_imdsv2_prevent_old_instance_metadata_api.png)