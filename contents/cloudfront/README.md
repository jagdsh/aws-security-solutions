# Cloudfront

- Content Delivery Network (CDN)
- Improves read performance, content is cached at the edge
- Improves users experience (Reduces Latency)
- 216 Point of Presence globally (edge locations)
- DDoS protection (because worldwide), integration with Shield, AWS Web Application Firewall

![CloudFront](./cloudfront.png)

[Source](https://aws.amazon.com/cloudfront/features/?nc=sn&loc=2)

## Origins

- S3 bucket
  - For distributing files and caching them at the edge
  - Enhanced security with CloudFront Origin Access Control (OAC)
  - OAC is replacing Origin Access Identity (OAI)
  - CloudFront can be used as an ingress (to upload files to S3)
- Custom Origin (HTTP)
  - Application Load Balancer
  - EC2 instance
  - S3 website (must first enable the bucket as a static S3 website)
  - Any HTTP backend you want

## High Level

![High Level](./cf_high_level.png)

## S3 as an Origin

- If you have enabled public access of the bucket using ACL for the bucket, you need to disable it so Origin Access Identity (OAI) is used.

![S3 as Origin](./cf_s3_origin.png)

## CloudFront SSL/TLS

[Cloud front with SSL / TLS](./cf_ssl_tls.png)

## CloudFront vs S3 Cross Region Replication

- CloudFront:
  - Global Edge network
  - Files are cached for a TTL (maybe a day)
  - Great for static content that must be available everywhere
- S3 Cross Region Replication:
  - Must be setup for each region you want replication to happen
  - Files are updated in near real-time
  - Read only
  - Great for dynamic content that needs to be available at low-latency in few regions

## ALB or EC2 as an origin

![EC2 as Origin](./cf_ec2_origin.png)

![ALB as Origin](./cf_alb_origin.png)

## Geo Restriction

- You can restrict who can access your distribution
  - Allowlist: Allow your users to access your content only if they're in one of the countries on a list of approved countries.
  - Blocklist: Prevent your users from accessing your content if they're in one of the countries on a list of banned countries.
- The “country” is determined using a 3rd party Geo-IP database
- Use case: Copyright Laws to control access to content

## Signed URL / Signed Cookies

- You want to distribute paid shared content to premium users over the world
- To Restrict Viewer Access, we can create a CloudFront Signed URL / Cookie
- How long should the URL be valid for?
  - Shared content (movie, music): make it short (a few minutes)
  - Private content (private to the user): you can make it last for years
- Signed URL = access to individual files (one signed URL per file)
- Signed Cookies = access to multiple files (one signed cookie for many files)

## Signed URL Diagram

![Signed Url Diagram](./cf_signed_url_diagram.png)

## Signed URL vs S3 Pre-Signed URL

CloudFront Signed URL:
• Allow access to a path, no matter the origin
• Account wide key-pair, only the root can manage it
• Can filter by IP, path, date, expiration
• Can leverage caching features

![Signed Url](./cf_signed_url.png)

S3 Pre-Signed URL:
• Issue a request as the person who pre-signed the URL
• Uses the IAM key of the signing IAM principal -> THe user who access it should have the same rights as the person who signed
• Limited lifetime

![Pre-Signed Url](./cf_pre-signed_url.png)

## Signed URL Process

- Two types of signers:
  - Either a trusted key group (recommended)
    - Can leverage APIs to create and rotate keys (and IAM for API security)
  - An AWS Account that contains a CloudFront Key Pair
    - Need to manage keys using the root account and the AWS console
    - Not recommended because you shouldn’t use the root account for this
- In your CloudFront distribution, create one or more trusted key groups
- You generate your own public / private key
  - The private key is used by your applications (e.g. EC2) to sign URLs
  - The public key (uploaded) is used by CloudFront to verify URLs
- Only 2048 bits keys are accepted

## Field Level Encryption

- Protect user sensitive information through application stack
- Adds an additional layer of security along with HTTPS
- Sensitive information encrypted at the edge close to user
- Uses asymmetric encryption
- Usage:
  - Specify set of fields in POST requests that you want to be encrypted (up to 10 fields)
  - Specify the public key to encrypt them

![Field Level Encryption](./cf_field_encryption.png)

## Origin Access Control with SSE-KMS

- OAC supports SSE-KMS natively (as requests are signed with Sigv4)
- Add a statement to the KMS Key Policy to authorize the OAC

![Origin Access Control](./origin_access_control_sse_kms.png)

## Origin Access Identity with SSE-KMS

- OAI doesn’t support SSE-KMS natively (only SSE-S3)
- Use Lambda@Edge to sign requests from CloudFront to S3
- Make sure to disable OAI for this to work

![Origin Identity Access](./origin_access_identity_sse_kms.png)

## Authorization Header

- Configure CloudFront distribution to forward the Authorization header using Cache Policy
- Not supported for S3 Origins

![Cloudfront Authorization Header](./cf_authorization_header.png)

## Restrict access to ALB

- Prevent direct access to your ALB or Custom Origins (only access through CloudFront)
- First, configure CloudFront to add a Custom HTTP Header to requests it sends to the ALB
- Second, configure the ALB to only forward requests that contain that Custom HTTP Header
- Keep the custom header name and value secret!

![Access Restrict to ALB](./cf_restrict_access_to_alb.png)

## Integration with Cognito

![Integration with Cognito](./integration_with_cognito.png)
