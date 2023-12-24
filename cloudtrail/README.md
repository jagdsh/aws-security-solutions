# Cloudtrail

- Provides governance, compliance and audit for your AWS Account
- CloudTrail is enabled by default!
- Get an history of events / API calls made within your AWS Account by:
  - Console
  - SDK
  - CLI
  - AWS Services
- Can put logs from CloudTrail into CloudWatch Logs or S3
- A trail can be applied to All Regions (default) or a single Region.
- If a resource is deleted in AWS, investigate CloudTrail first!

![Flow Diagram](./flow_diagram.png)

## Events

- Management Events:
  - Operations that are performed on resources in your AWS account
  - Examples:
  - Configuring security (IAM AttachRolePolicy)
  - Configuring rules for routing data (Amazon EC2 CreateSubnet)
  - Setting up logging (AWS CloudTrail CreateTrail)
  - By default, trails are configured to log management events.
  - Can separate Read Events (that don’t modify resources) from Write Events (that may modify resources)
- Data Events:
  - By default, data events are not logged (because high volume operations)
  - Amazon S3 object-level activity (ex: GetObject, DeleteObject, PutObject): can separate Read and Write Events
  - AWS Lambda function execution activity (the Invoke API)

## Insights

![Insights](./insights.png)

- Enable CloudTrail Insights to detect unusual activity in your account:
  - inaccurate resource provisioning
  - hitting service limits
  - Bursts of AWS IAM actions
  - Gaps in periodic maintenance activity
- CloudTrail Insights analyzes normal management events to create a baseline
- And then continuously analyzes write events to detect unusual patterns
  - Anomalies appear in the CloudTrail console
  - Event is sent to Amazon S3
  - An EventBridge event is generated (for automation needs)

## Retention

![Retention](./retention.png)

- Events are stored for 90 days in CloudTrail
- To keep events beyond this period, log them to S3 and use Athena


## Intercept API Calls

- Intercept any API call for particular patter

![Intercept API Call](./interept_api_calls.png)

## Amazon EventBridge + CloudTrail

![Cloudtrail Event Bridge ](./cloudtrail_event_bridge.png)

## (SysOps) Log File Integrity Validation

![Log File Integrity](./log_file_integrity.png)

- Digest Files:
  - References the log files for the last hour and contains a hash of each
  - Stored in the same S3 bucket as log files (different folder)
- Helps you determine whether a log file was modified/deleted after CloudTrail delivered it
- Hashing using SHA-256, Digital Signing using SHA-256 with RSA
- Protect the S3 bucket using bucket policy, versioning, MFA Delete protection, encryption, object lock
- Protect CloudTrail using IAM

## (SysOps) Integration with EventBridge

![Integration with EventBridge](./integration_event_bridge.png)

- Used to react to any API call being made in your account
- CloudTrail is not “real-time”:
  - Delivers an event within 15 minutes of an API call
  - Delivers log files to an S3 bucket every 5 minutes

## (SysOps) Organizations Trail

![Organizational Trails](./organizational_trails.png)

- A trail that will log all events for all AWS accounts in an AWS Organization
- Log events for management and member accounts
- Trail with the same name will be created in every AWS account (IAM permissions)
- Member accounts can’t remove or modify the organization trail (view only)

## (SysOps) CloudTrail to CloudWatch Metrics Filter

![Integration with Metric Filter](./integration_with_metrics_filter.png)

## Integration with Athena

![Integration with Athena](./integration_with_athena.png)

- You can use Athena to directly query CloudTrail Logs stored in S3
- Example: analyze operational activity for security and compliance
- *Can create Athena table directly from the CloudTrail Console*, then specify the S3 bucket location where the CloudTrail logs are stored

# Summary - Monitor Account Activity

- AWS Config Configuration History
  - Must have AWS Config Configuration Recorder on
- CloudTrail Event History
  - Search API history for past 90 days
  - Filter by resource name, resource type, event name, …
  - Filter by IAM user, assumed IAM role session name, or AWS Access Key
- CloudWatch Logs Insights
  - Search API history beyond the past 90 days
  - CloudTrail Trail must be configured to send logs to CloudWatch Logs
- Athena Queries
  - Search API history beyond the past 90 days