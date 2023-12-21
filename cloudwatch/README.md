
# Unified CloudWatch Agent

- For virtual servers (EC2 instances, on-premises servers, etc)
- Collect additional system-level metrics such as RAM, processes, used disk space, etc.
- Collect logs to send to CloudWatch Logs
- No logs from inside your EC2 instance will be sent to CloudWatch Logs without using an agent
- Centralized configuration using SSM Parameter Store
- Make sure IAM permissions are correct
- Default namespace for metrics collected by the Unified CloudWatch agent is CWAgent (can be configured/changed)

![Unified CW Agent](./unified_cw_agent.png)

*CloudWatchAgentServerPolicy - Logs:PutLogEvents, Logs:DescribeLogEvents, Logs:CreateLogStream, GetSSMParameters*
*CloudWatchAgentAdminPolicy  - Logs:PutLogEvents, Logs:DescribeLogEvents, Logs:CreateLogStream, GetSSMParameters, PutSSMParameters*

## Unified CloudWatch Agent – procstat Plugin

- Collect metrics and monitor system utilization of individual processes
- Supports both Linux and Windows servers
- Example: amount of time the process uses CPU, amount of memory the process uses
- Select which processes to monitor by
  - pid_file: name of process identification number (PID) files they create
  - exe: process name that match string you specify (RegEx)
  - pattern: command lines used to start the processes (RegEx)
- Metrics collected by procstat plugin begins with “procstat” prefix (e.g., procstat_cpu_time, procstat_cpu_usage, etc)

## Unified CloudWatch Agent - Troubleshooting

- CloudWatch Agent Fails to Start
  - Might be an issue with the configuration file
  - Check configuration file logs at ${\color{yellow}`/opt/aws/amazon-cloudwatch-agent/logs/configuration-validation.log`}$
- Can’t Find Metrics Collected by the CloudWatch Agent
  - Check you’re using the correct namespace (default: CWAgent)
  - Check the configuration file `amazon-cloudwatch-agent.json`

![CW JSON Agent](./cw-agent-json.png)

- CloudWatch Agent Not Pushing Log Events
  - Update to the latest CloudWatch Agent version
  - Test connectivity to the CloudWatch Logs endpoint ${\color{yellow}`logs.<region>.amazonaws.com`}$ (check SGs, NACLs, …)
  - Review account, region, and log group configurations
  - Check IAM Permissions
  - Verify the system time on the instance is correctly configured
- Check CloudWatch Agent logs at ${\color{yellow}`/opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log`}$

![CW IAM Policy](./cw-iam-policy.png)

# CloudWatch Logs

- *Log groups:* arbitrary name, usually representing an application
- *Log stream:* instances within application / log files / containers
- Can define log expiration policies (never expire, 1 day to 10 years…)
- CloudWatch Logs can send logs to:
  - Amazon S3 (exports)
  - Kinesis Data Streams
  - Kinesis Data Firehose
  - AWS Lambda
  - OpenSearch
- Logs are encrypted by default
- Can setup KMS-based encryption with your own keys

## Metric Filter

- Details needed for creating a Metric Filter
  - Namespace
  - Name
  - Value -> what value should be associated for every finding pattern
  - 
- Can access the Metric filter under Metrics tab in Dashboard
  - We can view graph of the metric in there
- We use the Metric filter to create alarms, if the value comes above certain threshold

## Sources

- SDK, CloudWatch Logs Agent, CloudWatch Unified Agent
- Elastic Beanstalk: collection of logs from application
- ECS: collection from containers
- AWS Lambda: collection from function logs
- VPC Flow Logs: VPC specific logs
- API Gateway
- CloudTrail based on filter
- Route53: Log DNS queries

## Insights

- Search and analyze log data stored in CloudWatch Logs
  - Example: find a specific IP inside a log, count occurrences of “ERROR” in your logs
- Provides a purpose-built query language
  - Automatically discovers fields from AWS services and JSON log events
  - Fetch desired event fields, filter based on conditions, calculate aggregate statistics, sort events, limit number of events…
  - Can save queries and add them to CloudWatch Dashboards
- Can query multiple Log Groups in different AWS accounts
- It’s a query engine, not a real-time engine -> You can only query older data

![Insights Dashboard](./cw_insights.png)

![Purpose-built query](./cw_query.png)

## S3 Export

![S3 Export](./cw_s3_export.png)

- Log data can take up to 12 hours to become available for export
- The API call is CreateExportTask
- Not near-real time or real-time, use Logs Subscriptions instead

## Subscriptions

- Get a real-time log events from CloudWatch Logs for processing and analysis
- Send to Kinesis Data Streams, Kinesis Data Firehose, or Lambda
- Subscription Filter – filter which logs are events delivered to your destination

![Cloud watch](./cw_subscription.png)

## Aggregation Multi-Account & Multi Region

![Cross Account & Cross Region logs Subscription](./cw_subscription_cross_account_cross_region.png)

- Cross-Account Subscription – send log events to resources in a different AWS account (KDS, KDF)

![Cross Account Subscription](./cw_subscription_cross_account.png)
![Cross Account Subscription IAM Pollicy](./cw_subscription_cross_account_policy.png)