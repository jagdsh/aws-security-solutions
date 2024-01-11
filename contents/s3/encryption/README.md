# Amazon S3 Encryption

## Object Encryption

- You can encrypt objects in S3 buckets using one of 4 methods
- Server-Side Encryption (SSE)
  - Server-Side Encryption with Amazon S3-Managed Keys (SSE-S3) – Enabled by
    Default - Encrypts S3 objects using keys handled, managed, and owned by AWS
  - Server-Side Encryption with KMS Keys stored in AWS KMS (SSE-KMS)
    - Leverage AWS Key Management Service (AWS KMS) to manage encryption keys
  - Server-Side Encryption with Customer-Provided Keys (SSE-C)
    - When you want to manage your own encryption keys
- Client-Side Encryption
- It’s important to understand which ones are for which situation for the exam

### Amazon S3 Encryption – SSE-S3

- Encryption using keys handled, managed, and owned by AWS
- Object is encrypted server-side
- Encryption type is AES-256
- Must set header "x-amz-server-side-encryption": "AES256"
- Enabled by default for new buckets & new objects

![S3 Encryption SSE-S3](./s3_encryption_sse_s3.png)

### Amazon S3 Encryption – SSE-KMS

- Encryption using keys handled and managed by AWS KMS (Key Management Service)
- KMS advantages: user control + audit key usage using CloudTrail
- Object is encrypted server side
- Must set header "x-amz-server-side-encryption": "aws:kms"

![S3 Encryption SSE-KMS](./s3_encryption_sse_kms.png)

### SSE-KMS Limitation

- If you use SSE-KMS, you may be impacted by the KMS limits
- When you upload, it calls the GenerateDataKey KMS API
- When you download, it calls the Decrypt KMS API
- Count towards the KMS quota per second (5500, 10000, 30000 req/s based on region)
- You can request a quota increase using the Service Quotas Console

![SSE-KMS Limitation](./sse_kms_limitation.png)

### Amazon S3 Encryption – SSE-C

- Server-Side Encryption using keys fully managed by the customer outside of AWS
- Amazon S3 does NOT store the encryption key you provide
- HTTPS must be used
- Encryption key must provided in HTTP headers, for every HTTP request made

![SSE-KMS Limitation](./s3_encryption_sse_c.png)

### Amazon S3 Encryption – Client-Side Encryption

- Use client libraries such as Amazon S3 Client-Side Encryption Library
- Clients must encrypt data themselves before sending to Amazon S3
- Clients must decrypt data themselves when retrieving from Amazon S3
- Customer fully manages the keys and encryption cycle

![S3 Encryption Client Side](./s3_encryption_client_side.png)

### Amazon S3 – Encryption in transit (SSL/TLS)

- Encryption in flight is also called SSL/TLS
- Amazon S3 exposes two endpoints:
  - HTTP Endpoint – non encrypted
  - HTTPS Endpoint – encryption in flight
- HTTPS is recommended
- HTTPS is mandatory for SSE-C
- Most clients would use the HTTPS endpoint by default

## S3 Encryption for Objects - Summary

- SSE-S3: encrypts S3 objects using keys handled & managed by AWS
- SSE-KMS: leverage KMS to manage encryption keys
  - Key usage appears in CloudTrail
  - objects made public can never be read
  - On s3:PutObject, make the permission kms:GenerateDataKey is allowed
- SSE-C: when you want to manage your own encryption keys
- Client-Side Encryption
- Glacier : all data is AES-256 encrypted, key under AWS control

### Amazon S3 – Force Encryption in Transit aws:SecureTransport

````json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws: s3: ::my-bucket/*",
```
      "Condition": {
        "Bool": {
          "aws: SecureTransport": "false"
        }
      }
```
    }
  ]
}
````

![Force Encryption in Transit](./s3_encryption_in_transit.png)

### Amazon S3 – Default Encryption vs. Bucket Policies

- SSE-S3 encryption is automatically applied to new objects stored in S3 bucket
- Optionally, you can “force encryption” using a bucket policy and refuse any API call to PUT an S3 object without encryption headers (SSE-KMS or SSE-C)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "s3:PutObject",
      "Principal": "*",
      "Resource": "arn:aws: s3:::my-bucket/*",
      "Condition": {
        "StringNotEquals": {
          "s3:x-amz-server-side-encryption": "aws: kms"
        }
      }
    }
  ]
}
```

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "s3:PutObject",
      "Principal": "*",
      "Resource": "arn:aws:53::: my-bucket/*",
      "Condition": {
        "Null": {
          "s3:x-amz-server-side-encryption-customer-algorithm": "true"
        }
      }
    }
  ]
}
```

- Note: Bucket Policies are evaluated before “Default Encryption”

## S3 Bucket Policies

### Force HTTPS

````json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "s3:*",
      "Principal": "*",
      "Resource": ["arn: aws: s3: ::my-bucket", "arn:aws: s3:::my-bucket/*"],
```
      "Condition": {
        "Bool": {
          "aws: SecureTransport": "false"
        }
      }
```
    }
  ]
}
````

### Force SSE-KMS

````json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3::: bucketname/*",
```
      "Condition": {
        "StringNotEquals": {
          "s3:X-amz-server-side-encryption": "aws: kms"
        }
      }
```
    },
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:PutObject",
      "Resource": "arn:aws: s3::: samplebucketname/*",
```
      "Condition": {
        "StringNotLikeIfExists": {
          "s3:x-amz-server-side-encryption-aws-kms-key-id": "arn:aws: kms:us-east-1:123456789012:key/1234abcd-12ab-34cd-56ef-1234567890ab"
        }
      }
```
    }
  ]
}
````

## S3 Bucket Key for SSE-KMS Encryption

- New setting to decrease
  - Number of API calls made to KMS from S3 by 99%
  - Costs of overall KMS encryption with Amazon S3 by 99%
- This leverages data keys
  - A “S3 bucket key” is generated
  - That key is used to encrypt KMS objects with new data keys
- You will see less KMS CloudTrail
  events in CloudTrail

![Bucket Key SSE KMS](./bucket_key_sse_kms.png)

## Large File Upload to S3 with KMS Key **[Troubleshooting]**

- When uploading a large file to S3 with KMS encryption, you’ll have to use S3 multi-part upload
- You must have the following permissions to the KMS Key:
  - kms:GenerateDataKey – allows you to encrypt each object part with a unique Data
    Key
  - kms:Decrypt – decrypt object parts before they can be assembled, then re-encrypt them with the KMS Key

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": ["kms: Decrypt", "kms: GenerateDataKey"],
    "Resource": [
      "arn: aws: kms: example-region-1:123456789012:key/111aa2bb-333c-4d44-5555-a111bb2c33dd"
    ]
  }
}
```

![Large File S3 KMS Key](./large_file_s3_kms_key.png)

## S3 Batch – Object Encryption

- S3 Batch – perform bulk operations on existing S3 objects with a single request (e.g., encrypt un-encrypted objects)
• S3 Inventory – to get the list of all objects and its associated metadata (select Encryption Status from optional fields)
• S3 Select or Athena – to filter and list only un-encrypted objects
• Note: S3 Batch Operations job must have access to the S3 bucket and the KMS Key

![Object Encryption](./object_encryption.png)

