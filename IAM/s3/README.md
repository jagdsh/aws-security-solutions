# IAM in S3

## How Authorization Works in Amazon S3

- User Context
  - Is the IAM principal authorized by the parent AWS account (IAM policy) ?
  - If the parent owns the bucket or object, then bucket policy/ACL or object ACL is evaluated (ACL are deprecated).
  - If the parent owns the bucket/object, it can grant permissions to its IAM principals using Identity-Based Policy or Resource-Based Policy
- Bucket Context
  - Evaluates the policies of the AWS account that owns the bucket (check for Explicit Deny)
- Object Context
  - Requester must have permission from the object owner (using Object ACL)
  - If bucket owner = object owner, then access granted using Bucket Policy
  - If bucket owner != object owner, then access granted using object owner ACL
  - If you want to own all objects in your bucket and only use Bucket Policy and IAM-Based Policies to grant access, enable Bucket Owner Enforced Setting for Object Ownership
  - Then Bucket and objects ACLs can’t be edited and are no longer considered for access

![Authorization in S3](./authorization_in_s3.png)

## Object Operation Request example: 3 accounts

- S3 verifies that the parent AWS account has given Jill permission to perform the requested operation
- S3 evaluates the bucket policy to determine if the bucket owner has explicitly denied Jill access to the object
- S3 evaluates the object ACL to determine if Jill has permission to access the object
  - Account 3 should have authorized to access the account 1
  - If Account 2 (2222222222) and Account 3 (333333333) are same Object ACL is not needed. so only bucket policy is considered.

![Object Operation Request](./object_operation_request.png)

## Bucket Operations vs Object Operations

- s3:ListBucket permission applies to arn:aws:s3:::test
- => bucket level permission
- s3:GetObject, s3:PutObject, s3:DeleteObject applies to arn:awn:s3:::test/\*
- => object level permission

![Object Operations](./s3_object_operation.png)

## Cross-Account Access to Objects in S3 Buckets

- Use one of the following to grant cross-account access to S3 objects:
- IAM Policies and S3 Bucket Policy
- IAM Policies and Access Control Lists (ACLs)
- ACLs only works if Bucket Owner Enforced setting = Disabled
- By default, all newly created buckets have Bucket Owner Enforced = Enabled
- Cross-Account IAM Roles

## IAM Policies and Resource-Based Bucket Policies

![Resource-Based Bucket policy](./resource_based_policy.png)

Note: Account B (Bob) can’t access objects
owned by Account A (Account B S3 Bucket)
unless explicitly authorized by Account A

- This is because there is a concept where the uploader will only have the access to the object when uploaded.
-

## Object Permissions in Cross-Account Setting

- Account A user can make sure it gives up object ownership by granting the object ownership to the Account B administrator
- Using ACL: with adding condition that requests include ACL-specific headers, that either:
- Grant full permissions explicitly (ex: x-amz-grant-full-control)
- Use Canned ACL (see next)
- Using S3 Object Ownership (bucket-level setting to disable ACLs)

![Object Permissions Cross-Account Policy](./object_cross_account_policy.png)

## Canned ACL Object Permissions in Cross-Account Setting

- You can require the x-amz-acl header with a Canned ACL granting full control permission to the bucket owner
- To require the x-amz-acl header in the request, specify the s3:x-amz-aclcondition key

```json
"Condition": {
    "StringEquals": {
        "s3:x-amz-acl": "bucket-owner-full-control"
    }
}
```

## S3 Canned ACL – Deep Dive

- Canned ACL are “Shortcuts ACLs”
- ACL are NOT recommended (& disabled by default since Apr2023)
- By default, enable “Object Ownership” so that only Bucket Policies, IAM Policies, SCP, and VPC Endpoint Policies control access to your S3 Bucket Objects
- The bucket owner will own all the objects

| Canned ACL                | Applies to      | Permissions Added to ACL                                                                                                              |
| ------------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| private                   | Bucket & Object | Owner gets FULL_CONTROL. No one else has access rights (default).                                                                     |
| public-read               | Bucket & Object | Owner gets FULL_CONTROL. AllUsers group gets READ access.                                                                             |
| public-read-write         | Bucket & Object | Owner gets FULL_CONTROL. AllUsers group gets READ and WRITE access. (not recommended)                                                 |
| aws-exec-read             | Bucket & Object | Owner gets FULL_CONTROL. EC2 gets READaccess to GET an Amazon Machine Image (AMI).                                                    |
| authenticated-read        | Bucket & Object | Owner gets FULL_CONTROL. AuthenticatedUsers group gets READ access.                                                                   |
| bucket-owner-read         | Object          | Object owner gets FULL_CONTROL. Bucket owner gets READ access. (S3 ignores it if you specify it when creating a bucket)               |
| bucket-owner-full-control | Object          | Both the object owner and the bucket owner get FULL_CONTROL over the object. (S3 ignores it if you specify it when creating a bucket) |
| log-delivery-write        | Bucket          | LogDelivery group gets WRITE and READ ACPpermissions on the bucket                                                                    |

## Cross-Account IAM Roles

- You can use cross-account IAM roles to centralize permission management when providing cross-account access to multiple services
- Example: access to S3 objects that are stored in multiple S3 buckets
- Bucket Policy is not required as the API calls to S3 come from within the account (through the assumed IAM role)

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": ["s3: GetObject", "s3:PutObject"],
    "Resource": "arn:aws:s3:::my-bucket/*"
  }
}
```

![Cross account](./cross_account.png)

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "sts:AssumeRole",
    "Resource": "arn:aws: iam: :AccountA: role/AccountARole"
  }
}
```

## Sample Bucket Policies Force HTTPS In Flight **[Troubleshooting]**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws: s3:::my-bucket/*",
      "Condition": {
        "Bool": {
          "aws: SecureTransport": "false"
        }
      }
    }
  ]
}
```

![Sample Bucket](./sample_bucket_https.png)

## S3 Bucket Policy – Restrict Public IP Address

![Restrict IP Address](./restrict_ip_adddress.png)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": "arn:aws:s3:::my-bucket/*",
      "Condition": {
        "NotIpAddress": {
          "aws:SourceIp": ["11.11.11.11/24"]
        }
      }
    }
  ]
}
```

## S3 Bucket Policy – Restrict by User ID

- Block traffic to the bucket unless request is from specified users (same AWS account)

![Restrict User Id Policy](./restrict_user_id_policy.png)

![Restrict User Id](./restrict_user_id.png)

## VPC Gateway Endpoint for Amazon S3

- No cost
- Only accessed by resources in the VPC where it’s created
- Make sure “DNS Support” is Enabled
- Keep on using the public DNS of Amazon S3
- Make sure Outbound rules of SG of EC2 instance allows traffic to S3

![VPC Gateway Endpoint](./vpc_gateway_endpoint.png)

## VPC Interface Endpoint for S3

- ENI(s) are deployed in your Subnets (Security Groups can be attached to ENIs)
- Can access from on-premises (VPN or Direct Connect)
- Costs $0.01 per hour per AZ
- The public hostname of a service will resolve to the private Endpoint Interface hostname
- Both VPC Setting “Enable DNS hostnames” and “Enable DNS Support” must be 'true’
- No “Private DNS name” option for VPC Interface Endpoint for S3

![VPC Interface Endpoint](./vpc_interface_endpoint.png)

- Using gateway endpoint wil only be accessed by resources in your VPC, not side to side VPN, VPC peering etc.

## Amazon S3 Security – VPC Endpoint Strategies

- Single VPC Architecture
  ![Single VPC Architecture](./single_vpc_architecture.png)

- Multi-VPC Centralized Architecture
  ![Multi-VPC Centralized Architecture](./multi_vpc_architecture.png)

## VPC Endpoint Restrictions aws:SourceVpc and aws:SourceVpce

- Restrict Access from specific VPCs

```json
{
  "Effect": "Deny",
  "Action": "s3:*",
  "Principal": "*",
  "Resource": ["arn:aws:s3:::my-bucket", "arn:aws:s3::: my-bucket/*"],
  "Condition": {
    "StringNotEquals": {
      "aws:SourceVpc": ["vpc-111bbb22"]
    }
  }
}
```

- Restrict Access from Specific VPC Endpoints

```json
{
  "Effect": "Deny",
  "Action": "s3:*",
  "Principal": "*",
  "Resource": ["arn: aws: s3: ::my-bucket", "arn:aws: s3:::my-bucket/*"],
  "Condition": {
    "StringNotEquals": {
      "aws: SourceVpce": ["vpce-1111111", "vpce-2222222"]
    }
  }
}
```

## IP Address Restrictions aws:SourceIp and aws:VpcSourceIp

- Restrict Access from Public IP Addresses

```json
{
  "Effect": "Deny",
  "Action": "s3:*",
  "Principal": "*",
  "Resource": ["arn:aws: s3:::my-bucket", "arn:aws: s3:::my-bucket/*"],
  "Condition": {
    "NotIpAddress": {
      "aws:SourceIp": ["192.0.2.0/24", "203.0.113.0/24"]
    }
  }
}
```

- Restrict Access from Private IP Addresses

```json
{
  "Effect": "Deny",
  "Action": "s3:*",
  "Principal": "*",
  "Resource": ["arn:aws: s3:::my-bucket", "arn:aws: s3: ::my-bucket/*"],
  "Condition": {
    "NotIpAddress": {
      "aws:VpcSourceIp": ["10.1.1.1/32", "172.1.1.1/32"]
    }
  }
}
```

## Regain Access to Locked S3 Buckets

- If you incorrectly configured your S3 bucket policy to deny access to everyone (Deny s3:_, Principal:_)
- You must delete the S3 bucket policy using the AWS account root user
- Note: Deny statements in IAM policies do not affect the root account

```json
{
  "Effect": "Deny",
  "Action": "s3:*",
  "Principal": "*",
  "Resource": ["arn:aws: s3:::my-bucket", "arn:aws: s3: ::my-bucket/*"]
}
```

![Regain Access to Locked S3 Buckets](./regain_access_locked.png)

## Bucket settings for Block Public Access

![Block S3 Access](./block_s3_access.png)

- These settings were created to prevent company data leaks
- If you know your bucket should never be public, leave these on
- Can be set at the account leave

## S3 – Access Points

![Access Point](./access_point.png)

- Access Points simplify security management for S3 Buckets
- Each Access Point has:
  - its own DNS name (Internet Origin or VPC Origin)
  - an access point policy (similar to bucket policy) – manage security at scale

## S3 – Access Points – VPC Origin

- We can define the access point to be accessible only from within the VPC
- You must create a VPC Endpoint to access the Access Point (Gateway or Interface Endpoint)
- The VPC Endpoint Policy must allow access to the target bucket and Access Point

![Access Point VPC Origin](./vpc_origin_access_point.png)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Principal": "*",
      "Action": ["s3:GetObject"],
      "Effect": "Allow",
      "Resource": [
        "arn:aws: s3::: awsexamplebucket1/*",
        "arn:aws: s3:us-west-2:123456789012:accesspoint/example-vpc-ap/object/*"
      ]
    }
  ]
}
```

## S3 – Multi-Region Access Points

- Provide a global endpoint that span S3 buckets in multiple AWS regions
- Dynamically route requests to the nearest S3 bucket (lowest latency)
- Bi-directional S3 bucket replication rules are created to keep data in sync across regions
- Failover Controls – allows you to shift requests across S3 buckets in different AWS regions within minutes (Active-Active or Active-Passive)

![Multi-Region Access Points](./access_point_multi_region.png)

![Multi-Region Access Points Diagram](./access_point_multi_region_diagram.png)

## Multi-Region Access Points – Failover Controls

Active/Passive Failover Controls

![Multi-Region Access Points - Failover](./access_point_failover_controls.png)

Works with active/active or active/passive setups

## What is CORS?

- Cross-Origin Resource Sharing (CORS)
- Origin = scheme (protocol) + host (domain) + port
  - example: ${\color{blue}https://www.example.com}$ (implied port is 443 for HTTPS, 80 for HTTP)
- Web Browser based mechanism to allow requests to other origins while visiting the main origin
- Same origin: ${\color{orange}http://example.com}$/app1 & ${\color{orange}http://example.com}$/app2
- Different origins: ${\color{green}http://www.example.com}$ & ${\color{blue}http://other.example.com}$
- The requests won’t be fulfilled unless the other origin allows for the requests, using CORS Headers (example: Access-Control-Allow-Origin)

![CORS](./cors.png)

## Amazon S3 – CORS **[Troubleshooting]**

- If a client makes a cross-origin request on our S3 bucket, we need to enable the correct CORS headers
- It’s a popular exam question
- You can allow for a specific origin or for \* (all origins)

![S3 CORS](./cors_s3.png)


![S3 CORS Example Index](../../sample_codes/s3/index.html)