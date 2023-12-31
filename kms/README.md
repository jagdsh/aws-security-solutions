# AWS KMS (Key Management Service)

- Anytime you hear “encryption” for an AWS service, it’s most likely KMS
- Easy way to control access to your data, AWS manages keys for us
- Fully integrated with IAM for authorization
- Seamlessly integrated into:
  - Amazon EBS: encrypt volumes
  - Amazon S3: Server-side encryption of objects
  - Amazon Redshift: encryption of data
  - Amazon RDS: encryption of data
  - Amazon SSM: Parameter store
  - Etc…
- But you can also use the CLI / SDK

## KMS – KMS Key Types

- Symmetric (AES-256 keys)
  - First offering of KMS, single encryption key that is used to Encrypt and Decrypt
  - AWS services that are integrated with KMS use Symmetric KMS keys
  - Necessary for envelope encryption
  - You never get access to the KMS key unencrypted (must call KMS API to use)
- Asymmetric (RSA & ECC key pairs)
  - Public (Encrypt) and Private Key (Decrypt) pair
  - Used for Encrypt/Decrypt, or Sign/Verify operations
  - The public key is downloadable, but you can’t access the Private Key unencrypted
  - Use case: encryption outside of AWS by users who can’t call the KMS API

## Types of KMS Keys

- Customer Managed Keys
  - Create, manage and use, can enable or disable
  - Possibility of rotation policy (new key generated every year, old key preserved)
  - Can add a Key Policy (resource policy) & audit in CloudTrail
  - Leverage for envelope encryption
- AWS Managed Keys
  - Used by AWS service (aws/s3, aws/ebs, aws/redshift)
  - Managed by AWS (automatically rotated every 1 years)
  - View Key Policy & audit in CloudTrail
- AWS Owned Keys
  - Created and managed by AWS, use by some AWS services to protect your resources
  - Used in multiple AWS accounts, but they are not in your AWS account
  - You can’t view, use, track, or audit

| KMS Key                       |  Customer Managed Key   |     AWS Managed Key      | AWS Owned Key |
| ----------------------------- | :---------------------: | :----------------------: | :-----------: |
| Can view metadata?            |   :white_check_mark:    |    :white_check_mark:    |      :x:      |
| Can manage?                   |   :white_check_mark:    |           :x:            |      :x:      |
| Used only for my AWS account? |   :white_check_mark:    |    :white_check_mark:    |      :x:      |
| Automatic Rotation            | Optional (every 1 year) | Required (every 1 years) |    Varies     |

## KMS Key Material Origin

- Identifies the source of the key material in the KMS key
- Can’t be changed after creation
- KMS (AWS_KMS) – default
  - AWS KMS creates and manages the key material in its own key store
- External (EXTERNAL)
  - You import the key material into the KMS key
  - You’re responsible for securing and managing this key material outside of AWS
- Custom Key Store (AWS_CLOUDHSM)
  - AWS KMS creates the key material in a custom key store (CloudHSM Cluster)

## KMS Key Source – Custom Key Store (CloudHSM)

- Integrate KMS with CloudHSM cluster as a Custom Key Store
- Key materials are stored in a CloudHSM cluster that you own and manage
- The cryptographic operations are performed in the HSMs
- Use cases:
  - You need direct control over the HSMs
  - KMS keys needs to be stored in a dedicated HSMs

![Custom Key Store](./custom_keystore.png)

- At least 2 active HSMs

## KMS Key Source - External

- Import your own key material into KMS key, Bring Your Own Key (BYOK)
- You’re responsible for key material’s security, availability, and durability outside of AWS
- Must be 256-bit Symmetric key (Asymmetric is NOT supported)
- Can’t be used with Custom Key Store (CloudHSM)
- Manually rotate your KMS key (Automatic Key Rotation is NOT supported)

![Key Source External](./key_source_external.png)

## KMS Multi-Region Keys

- Identical KMS keys in different AWS Regions that can be used interchangeably
- Multi-Region keys have the same key ID, key material, automatic rotation…
- Encrypt in one Region and decrypt in other Regions
- No need to re-encrypt or making cross-Region API calls
- KMS Multi-Region are NOT global (Primary + Replicas)
- Each Multi-Region key is managed independently
- Use cases: global client-side encryption, encryption on Global DynamoDB, Global Aurora

![KMS multi region keys](./kms_multi_region.png)

## DynamoDB Global Tables and KMS Multi-Region Keys Client-Side encryption

- We can encrypt specific attributes client-side in our DynamoDB table using the Amazon DynamoDB Encryption Client
- Combined with Global Tables, the client-side encrypted data is replicated to other regions
- If we use a multi-region key, replicated in the same region as the DynamoDB Global table, then clients in these regions can use low-latency API calls to KMS in their region to decrypt the data client-side
- Using client-side encryption we can protect specific fields and guarantee only decryption if the client has access to an API key

![KMS DynamoDB Global Tables](./kms_dynamodb_client_encryption.png)

## Global Aurora and KMS Multi-Region Keys Client-Side encryption

- We can encrypt specific attributes client-side in our Aurora table using the AWS Encryption SDK
- Combined with Aurora Global Tables, the client-side encrypted data is replicated to other regions
- If we use a multi-region key, replicated in the same region as the Global Aurora DB, then clients in these regions can use low-latency API calls to KMS in their region to decrypt the data client-side
- Using client-side encryption we can protect specific fields and guarantee only decryption if the client has access to an API key, we can protect specific fields even from database admins

![KMS Aurora Global Tables](./kms_aurora_client_encryption.png)

## How does KMS work? API – Encrypt and Decrypt

![KMS Encrypt and Decrypt](./kms_encrypt_decrypt.png)

## Envelope Encryption

- KMS Encrypt API call has a limit of 4 KB
- If you want to encrypt >4 KB, we need to use Envelope Encryption
- The main API that will help us is the GenerateDataKey API
- For the exam: anything over 4 KB of data that needs to be encrypted
  must use the Envelope Encryption == GenerateDataKey API

### Deep dive into Envelope Encryption GenerateDataKey API

![KMS Encrypt and Decrypt](./kms_envelope.png)

### Deep dive into Envelope Encryption Decrypt envelope data

![KMS Envelope Decrypt](./kms_envelope_decrypt.png)

## Encryption SDK

- The AWS Encryption SDK implemented Envelope Encryption for us
- The Encryption SDK also exists as a CLI tool we can install
- Implementations for Java, Python, C, JavaScript
- Feature - Data Key Caching:
  - re-use data keys instead of creating new ones for each encryption
  - Helps with reducing the number of calls to KMS with a security trade-off
  - Use LocalCryptoMaterialsCache (max age, max bytes, max number of messages)

## KMS Symmetric – API Summary

- Encrypt: encrypt up to 4 KB of data through KMS
- GenerateDataKey: generates a unique symmetric data key (DEK)
- returns a plaintext copy of the data key
- AND a copy that is encrypted under the CMK that you specify
- GenerateDataKeyWithoutPlaintext:
- Generate a DEK to use at some point (not immediately)
- DEK that is encrypted under the CMK that you specify (must use Decrypt later)
- Decrypt: decrypt up to 4 KB of data (including Data Encryption Keys)
- GenerateRandom: Returns a random byte string

## Key Rotation

### KMS Automatic Key Rotation

- AWS-managed KMS Keys: automatically rotated every 1 year
- For Customer-Managed KMS Key
  - Automatic key rotation is optionally enabled, will happen every 1 year
  - Previous key is kept active so you can decrypt old data
  - New Key has the same KMS Key ID (only the backing key is changed)

![Key Rotation](./kms_key_rotation.png)

### KMS Manual Key Rotation (for Customer-Managed KMS Key and Imports)

- When you want to rotate key every 90 days, 180 days, etc
- New Key has a different KMS Key ID
- Keep the previous key active so you can decrypt old data
- Better to use aliases in this case (to hide the change of key for the application)
- Good solution to rotate KMS Key that are not eligible for automatic rotation (like asymmetric CMK)

![Manual Key Rotation](./kms_manual_key_rotation.png)

## KMS Alias Updating

- Better to use aliases in this case (to hide the change of key for the
  application)

![KMS Alias Updating](./kms_alias.png)

## KMS Key Deletion

- Generated Keys (from within KMS)
  - No expiration date
  - Cannot be deleted immediately, mandatory 7 to 30 days waiting period
    - You can cancel key deletion during the waiting period
    - During the waiting period, the KMS Key cannot be used for Encrypt / Decrypt
    - Everything will be deleted at the end of the waiting period
  - You may manually disable it immediately instead (to re-enable it later)
- Imported Keys:
  - You may set an expiration period on the Key
    - KMS will delete the key material
    - You can also delete the key material on demand
    - The metadata is kept so you can re-import in the future
  - You may manually disable it and schedule for deletion (everything is deleted)
- AWS managed keys or AWS owned keys cannot be deleted

### KMS Key Deletion – CloudWatch Alarm

- Use CloudTrail, CloudWatch Logs, CloudWatch Alarms and SNS to be notified when someone tries to use a CMK that’s ”Pending deletion” in a cryptographic operation (Encrypt, Decrypt, …)

![Key Deletion](./kms_key_deletion.png)

### KMS Key Deletion – Notifications

- To be notified of Keys being deleted or disabled
- Using CloudTrail + EventBridge

![Key Deletion Notification](./kms_key_deletion_notification.png)

### KMS Multi Region Key Deletion

- Deleting Replica Key
  - Less risky, can always be re-created from the Primary Key (if it exists)
  - Must be scheduled (7 to 30 days)
- Deleting Primary Key
  - Cannot happen until all Replicas have been deleted
  - If you want to delete a Primary Key but keep Replicas, promote another one as Primary and then delete the “old Primary Key”
  - Must be scheduled (7 to 30 days after the replicas are deleted)

![Multi Region Deletion Notification](./kms_multi_region_key_deletion.png)

## KMS Key Policies

- Control access to KMS keys, “similar” to S3 bucket policies
- Difference: you cannot control access without them
- Default KMS Key Policy:
  - Created if you don’t provide a specific KMS Key Policy
  - Complete access to the key to the root user = entire AWS account
- Custom KMS Key Policy:
  - Define users, roles that can access the KMS key
  - Define who can administer the key
  - Useful for cross-account access of your KMS key

### Default KMS Key Policy

- It gives the AWS account that owns the KMS key, full access to the KMS key
- KMS Key Policy does NOT automatically give permission to the account or any of its users
- Allows the account to use IAM policies to allow access to the KMS key, in addition to the key policy

**Default KMS Key Policy**:

```json
{
  "Effect": "Allow",
  "Action": "kms: *",
  "Principal": {
    "AWS": "arn:aws: iam:: 123456789012:root"
  },
  "Resource": "*"
}
```

### KMS Key Policies - Comparison

- AWS Owned Keys
  - You cannot view or change the Key Policy
- AWS Managed Keys (e.g., aws/ebs)
  - You can view the Key Policy
  - You cannot change the Key Policy
- AWS Customer Managed Keys
  - You can view the Key Policy
  - You can edit the Key Policy

**Key Policy Comparison**:

|      | AWS Owned Keys |  AWS Managed Keys  | AWS Customer Managed Keys |
| ---- | :------------: | :----------------: | :-----------------------: |
| View |      :x:       | :white_check_mark: |    :white_check_mark:     |
| Edit |      :x:       |        :x:         |    :white_check_mark:     |

```json
{
  "Version": "2012-10-17",
  "Id": "auto-ebs-2",
  "Statement": [
    {
      "Sid": "Allow access through EBS for all principals in the account that are authorized to use EBS",
      "Effect": "Allow",
      "Principal": {
        "AWS": "*"
      },
      "Action": [
        "kms: Encrypt",
        "kms: Decrypt",
        "kms: ReEncrypt*",
        "kms: GenerateDataKey*",
        "kms: CreateGrant",
        "kms: DescribeKey"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "kms: CallerAccount": "123456789012",
          "kms:ViaService": "ec2.us-east-1.amazonaws.com"
        }
      }
    },
    {
      "Sid": "Allow direct access to key metadata to the account",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws: iam::123456789012: root"
      },
      "Action": [
        "kms: Describe*",
        "kms: Get*",
        "kms: List*",
        "kms: RevokeGrant"
      ],
      "Resource": "*"
    }
  ]
}
```

Key Policy for AWS Managed Key (aws/ebs)

### Custom KMS Key Policy – Allow Admins

- KMS Key administrators have permissions to manage the KMS key
- KMS Key administrators cannot use the KMS Key Cryptographic Operations (Encrypt / Decrypt…)
- You can add IAM Users / Roles as KMS Key administrators

```json
{
  "Sid": "Allow access for Key Administrators",
  "Effect": "Allow",
  "Principal": {
    "AWS": [
      "arn:aws: iam:: 123456789012:user/KMSAdminUser",
      "arn:aws: iam:: 123456789012: role/KMSAdminRole"
    ]
  },
  "Action": [
    "kms: Create*",
    "kms: Describe*",
    "kms: Enable*",
    "kms: List*",
    "kms: Put*",
    "kms: Update*",
    "kms: Revoke*",
    "kms: Disable*",
    "kms: Get*",
    "kms: Delete*",
    "kms: TagResource",
    "kms: UntagResource",
    "kms: ScheduleKeyDeletion",
    "kms: CancelKeyDeletion"
  ],
  "Resource": "*"
}
```

### Custom KMS Key Policy – Allow Users to Directly Use the KMS Key

- Allows IAM Users / Roles to use the KMS Key directly
- IAM Users / Roles don’t need IAM Policies if the KMS Key is in the same account
- The KMS Key explicitly authorizes the IAM Principal
- Alternative is Default KMS Key + IAM Policy

```json
{
  "Sid": "Allow use of the key",
  "Effect": "Allow",
  "Principal": {
    "AWS": [
      "arn:aws: iam:: 111122223333:user/ExampleUser",
      "arn:aws: iam:: 111122223333: role/ExampleRole",
      "arn:aws: iam::444455556666: root"
    ]
  },
  "Action": [
    "kms: Encrypt",
    "kms: Decrypt",
    "kms: ReEncrypt*",
    "kms: GenerateDataKey*",
    "kms: DescribeKey"
  ],
  "Resource": "*"
}
```

## KMS Grants

- Allows you to grant access to specific AWS KMS keys to other AWS accounts and IAM Users / Roles within your AWS account
- Often used for temporary permissions
- Can be created for a variety of operations, including encrypt, decrypt, sign,
  and verify, as well as creating more grants
- Grant are for one KMS Key only, and one or more IAM Principal
- Once granted, a principal can perform any operation as specified in the Grant
- Grants do NOT expire automatically, you must delete them manually
- You don’t need to change KMS Key Policy or IAM Policy

### Creating a KMS Key Grant

- Using AWS CLI

```bash
$ aws kms create-grant --key-id 1234abcd-12ab-34cd-56ef-1234567890ab \
--grantee-principal arn:aws: iam:: 123456789012:user/ExampleUser \
--operations Decrypt \
--retiring-principal arn:aws: iam:: 123456789012: role/ExampleRole \
-constraints EncryptionContextSubset={Department=IT}
```

- Make sure to delete a KMS Key Grant when you’re done using it
- For now, there’s no support in the AWS Console

### KMS Grants – AWS Service Usage

- Grants are commonly used by AWS services that integrate with AWS KMS to encrypt your data at rest
- The AWS service creates a Grant on behalf of a user in the account, uses its permissions, and retires the grant as soon as its task is complete
- Users must have the CreateGrant IAM permission

![KMS Grant Service](./kms_grant_service_usage.png)

### Custom KMS Key Policy – Grants for AWS Services Use Cases

- Use this KMS Key Policy with:
  - Amazon EBS and Amazon EC2 to attach an encrypted EBS volume to an EC2 instance
  - Amazon Redshift to launch an encrypted cluster
  - AWS services integrated with AWS KMS that use grants to create, manage, or use encrypted resources with those services

```json
{
  "Sid": "Allow attachment of persistent resources",
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws: iam: :123456789012:user/ExampleUser"
  },
  "Action": ["kms: CreateGrant", "kms: ListGrants", "kms: RevokeGrant"],
  "Resource": "*",
  "Condition": {
    "Bool": {
      "kms: GrantIsForAWSResource": true
    }
  }
}
```

KMS Key Policy

## **[Troubleshooting]**: Can’t Start an EC2 Instance with Encrypted EBS Volume

- Reasons:
  - KMS key might be disabled or deleted
  - EBS service might not have the required permissions to use KMS key
- Resolution:
  - Make sure the KMS key is exists and enabled
  - Make sure that EBS service has the require permissions to create KMS Grants in behalf of the specified principal
  - kms:GrantIsForAWSResource – allows AWS services to create Grants (e.g., EBS)

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws: iam:: 123456789012:user/ExampleUser"
  },
  "Action": "kms:CreateGrant",
  "Resource": "*",
  "Condition": {
    "Bool": {
      "kms: GrantIsForAWSResource": true
    }
  }
}
```

### Condition Keys – kms:ViaService

- kms:ViaService – limits the use of a KMS key to requests from specified AWS services
- Example: the following key policy allow usage of the KMS Key through EC2 or RDS in us-west-2 on behalf of the ExampleUser
- IAM user must be authorized to use the KMS Key and Grant it to the AWS service
- Can be used with AWS Managed Keys (e.g., aws/ebs)

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws: iam:: 123456789012:user/ExampleUser"
  },
  "Action": [
    "kms: Encrypt",
    "kms: Decrypt",
    "kms: ReEncrypt*",
    "kms: GenerateDataKey*",
    "kms: CreateGrant",
    "kms: ListGrants",
    "kms: DescribeKey"
  ],
  "Resource": "*",
  "Condition": {
    "StringEquals": {
      "kms:ViaService": [
        "ec2.us-west-2.amazonaws.com",
        "rds.us-west-2.amazonaws.com"
      ]
    }
  }
}
```

### Condition Keys – kms:CallerAccount

- kms:CallerAccount – Allow or deny access to all identities
  (IAM users and roles) in an AWS account
- Example: the following KMS Key policy is the Key policy for AWS Managed Key for Amazon EBS (aws/ebs)

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "*"
  },
  "Action": [
    "kms: Encrypt",
    "kms:Decrypt",
    "kms: ReEncrypt*",
    "kms: GenerateDataKey*",
    "kms: CreateGrant",
    "kms: DescribeKey"
  ],
  "Resource": "*",
  "Condition": {
    "StringEquals": {
      "kms: CallerAccount": "123456789012",
      "kms:ViaService": "ec2.us-west-2.amazonaws.com"
    }
  }
}
```

AWS Managed Key Policy for Amazon EBS (aws/ebs)

## KMS Key Authorization Process

![KMS Key Authorization](./kms_key_authorization.png)

- Key Policy or Key Grants must have explicit Allow
- IAM Policies, SCP, and VPC Endpoint Policies

### KMS Key Authorization Process – Example 1

![KMS Key Authorization Eg.1](./kms_authorization_example_1.png)

- Default KMS Key Policy (grants access to account)
- Alice makes API calls in the same account
- Alice is missing an IAM policy
- Alice is denied

### KMS Key Authorization Process – Example 2

![KMS Key Authorization Eg.2](./kms_authorization_example_2.png)

- KMS Key Policy in Account 2 allows for all Principals in Account 1
- Because it’s cross account, an IAM Policy in Account 1 must be used to authorise usage of KMS Key in Account 2

## KMS Key – Cross-Account Access

KMS Key Policy for Account A

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws: iam: :123456789012: root",
    "Action": [
      "kms: Encrypt",
      "kms: Decrypt",
      "kms: ReEncrypt*",
      "kms: GenerateDataKey*",
      "kms :DescribeKey"
    ],
    "Resource": "*"
  }
}
```

IAM Policy for Account B

```json
{
  "Effect": "Allow",
  "Action": [
    "kms: Encrypt",
    "kms: Decrypt",
    "kms :ReEncrypt*",
    "kms: GenerateDataKey*",
    "kms:DescribeKey"
  ],
  "Resource": "arn:aws: kms:us-west-2:111122223333: key/1234abcd-12ab-34cd-56e f+1234567890ab"
}
```

![Cross Account Access](./kms_cross_account_access.png)

### KMS Key – Cross-Account Access Usage of External KMS Keys with AWS Services

KMS Key Policy for account A

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws: iam::123456789012: root",
    "Action": [
      "kms: Encrypt",
      "kms: Decrypt",
      "kms: ReEncrypt*",
      "kms: GenerateDataKey*",
      "kms: DescribeKey",
      "kms: CreateGrant",
      "kms :ListGrants",
      "kms: RevokeGrant"
    ],
    "Resource": "*"
  }
}
```

IAM Policy for account B

```json
{
  "Effect": "Allow",
  "Action": [
    "kms: Encrypt",
    "kms:Decrypt",
    "kms :ReEncrypt*",
    "kms: GenerateDataKey*",
    "kms: DescribeKey",
    "kms: CreateGrant",
    "kms:ListGrants",
    "kms: RevokeGrant"
  ],
  "Resource": "arn: aws: kms: us-west-2:111122223333: key/1234abcd-12ab-34cd-56e f-1234567890ab AM Policy"
}
```

![Cross Account Access external keys](./kms_cross_account_external_keys.png)

### KMS Key – Cross-Account Access Through assuming an IAM Role

KMS Key Policy for account A

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws: iam::111122223333:root"
  },
  "Action": [
    "kms: Encrypt",
    "kms :Decrypt",
    "kms :ReEncrypt*",
    "kms: GenerateDataKey*",
    "kms :DescribeKey"
  ],
  "Resource": "*"
}
```

IAM assume Policy for for account B

```json
{
  "Effect": "Allow",
  "Action": [
    "kms: Encrypt",
    "kms: Decrypt",
    "kms: ReEncrypt*",
    "kms: GenerateDataKey*",
    "kms: DescribeKey"
  ],
  "Resource": "arn:aws: kms:us-west-2:111122223333: key/1234abcd-12ab-34cd-56e f-1234567890ab"
}
```

![Cross-Account Access IAM Assume role](./kms_cross_account_assume_iam_role.png)

### Sharing KMS Encrypted RDS DB Snapshots

- You can share RDS DB snapshots encrypted with KMS CMK with other accounts, but must first share the KMS CMK with the target account using Key Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": ["arn:aws: iam::444455556666: root"]
      },
      "Action": [
        "kms: CreateGrant",
        "kms: Encrypt",
        "kms: Decrypt",
        "kms: ReEncrypt*",
        "kms: GenerateDataKey*",
        "kms: DescribeKey"
      ],
      "Resource": "*"
    }
  ]
}
```

![KMS Encrypted RDS](./kms_encrypted_rds.png)

## Asymmetric Encryption

- An encryption process that uses a pair of related keys (public & private) to encrypt and decrypt data
- Public Key can be shared, Private Key must be kept secret
- KMS supports 3 types of asymmetric KMS keys:
  - RSA KMS Keys – encryption/decrytion or signing/verification
  - Elliptic Curve (ECC) KMS Keys – signing and verification
  - SM2 KMS Keys (China Regions only) – encryption/decrytion or signing/verification
- Private Keys never leaves AWS KMS unencrypted

![Asymmetric Encryption](./asymmetric_encryption.png)

## Digital Signing with KMS Asymmetric

- Helps you verify the integrity of messages or files sent across different systems
- Verify that file has not been tampered with in transit
- Public Key used to verify the signature while Private Key used in the signing process
- Use cases: document e-signing, secure messaging, authentication/authorization tokens, trusted source code, e-commerce transactions

![Digital Signing](./kms_asymmetric_digital_siging.png)

### KMS API Calls Limits & Data Key Caching

- When your application makes multiple API calls to KMS and you hit service limits (requests per second limit)…
- Data Key Caching allows you to reuse data keys that protect your data
- Instead of generating a new data key for each encryption operation
- Reduce latency, improve throughput, reduce cost, stay within service limits, …
- Implemented using AWS Encryption SDK
- Note: encryption best practices discourages reuse of data keys (tradeoff cost / security)

![KMS API Calls Limits](./kms_api_call_limits.png)

### Changing The KMS Key For An Encrypted EBS Volume

- You can’t change the encryption keys used by an EBS volume
- Create an EBS snapshot and create a new EBS volume and specify the new KMS key

![KMS Key Encrypted EBS](./kms_change_key_ebs.png)

### Automate Cross-Account EBS KMS-Encrypted Snapshot Copies

![Automate Cross-Account EBS](./kms_cross_account_ebs.png)

### KMS Key Policy in Target Account

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["kms: RevokeGrant", "kms: CreateGrant", "kms:ListGrants"],
      "Resource": [
        "arn: aws: kms: us-east-1:111111111111: key/1234abcd-12ab-34cd-56ef-1234567890ab",
        "arn:aws: kms:us-east-1:222222222222: key/4567dcba-23ab-34cd-56ef-0987654321yz"
      ],
      "Condition": {
        "Bool": {
          "kms: GrantIsForAWSResource": "true"
        }
      }
    },
    {
      "Effect": "Allow",
      "Action": [
        "kms: Encrypt",
        "kms: Decrypt",
        "kms :ReEncrypt*",
        "kms: GenerateDataKey*",
        "kms: DescribeKey"
      ],
      "Resource": [
        "arn: aws: kms: us-east-1:111111111111: key/1234abcd-12ab-34cd-56ef-1234567890ab",
        "arn: aws: kms:us-east-1:222222222222: key/4567dcba-23ab-34cd-56ef-0987654321yz"
      ]
    }
  ]
}
```

## EBS Encryption – Account level setting

- New Amazon EBS volumes aren’t encrypted by default
- There’s an account-level setting to encrypt automatically new EBS volumes and Snapshots
- This setting needs to be enabled on a per-region basis

![EBS Encryption Account setting](./kms_ebs_encryption.png)

## Encrypt Un-encrypted EFS File System

- You can’t encrypt an existing un-encrypted EFS File System
- Create a new encrypted EFS File System and migrate the files using AWS DataSync

![Encrypt EFS File System](./encrypt_efs_file_system.png)

## ABAC with KMS

- Control access to your KMS Keys based on tags and aliases

![ABAC KMS](./kms_abac.png)

## KMS with SSM Parameter Store

- SSM Parameter Store uses KMS to encrypt/decrypt parameter values of type Secure String
- Two types of Secure String Parameters:
  - Standard – all parameters encrypted using the same KMS key
  - Advanced – each parameter encrypted with a unique data key(Envelope Encryption)
- Specify the KMS key or use AWS Managed Key (aws/ssm)
- Works only with Symmetric KMS Keys
- Encryption process takes place in AWS KMS
- Note: you must have access to both the KMS key and the parameter in SSM Parameter Store

![KMS SSM Parameter](./ssm_parameter_store.png)
