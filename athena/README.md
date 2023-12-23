# Amazon Athena (Powerful tool)

- Serverless query service to analyze data stored in Amazon S3
- Uses standard SQL language to query the files (built on Presto)
- Supports CSV, JSON, ORC, Avro, and Parquet
- Pricing: $5.00 per TB of data scanned
- Commonly used with Amazon Quicksight for reporting/dashboards
- Use cases: Business intelligence / analytics / reporting, analyze & query VPC Flow Logs, ELB Logs, CloudTrail trails, etc...
- Exam Tip: analyze data in S3 using serverless SQL, use Athena


![Athena And Quick sights](./athena_quicksights.png)

## Performance Improvement

- Use columnar data for cost-savings (less scan)
  - Apache Parquet or ORC is recommended
  - Huge performance improvement
  - Use Glue to convert your data to Parquet or ORC
- Compress data for smaller retrievals (bzip2, gzip, lz4, snappy, zlip, zstd…)
- Partition datasets in S3 for easy querying on virtual columns
  - s3://yourBucket/pathToTable
                    /<PARTITION_COLUMN_NAME>=<VALUE>
                      /<PARTITION_COLUMN_NAME>=<VALUE>
                        /<PARTITION_COLUMN_NAME>=<VALUE>
                          /etc…
  - Example: s3://athena-examples/flight/parquet/year=1991/month=1/day=1/
- Use larger files (> 128 MB) to minimize overhead


## Federated Query

![Federated Query](./athena_federated_query.png)

- Allows you to run SQL queries across data stored in relational, non-relational, object, and custom data sources (AWS or on-premises)
- Uses Data Source Connectors that run on AWS Lambda to run Federated Queries (e.g., CloudWatch Logs, DynamoDB, RDS, …)
- Store the results back in Amazon S3

## Practical Implementation

- First Query Result S3 bucket
- Then the Data Source -> Create a database using the following sample query 

![S3 Access logs Athena](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-s3-access-logs-to-identify-requests.html)
![S3 Access logs Athena](../sanple_codes/s3-advanced/athena-s3-access-logs.sql)

## Troubleshooting

- Insufficient Permissions When using Athena with QuickSight
- Validate QuickSight can access S3 buckets used by Athena
- If the data in the S3 buckets is encrypted using AWS KMS key (SSE-KMS), then QuickSight IAM role must be granted access to decrypt with the key (kms:Decrypt)
- arn:aws:iam::<account_id>:role/service-role/aws-quicksight-s3-consumers-role-v0 (Default)
- arn:aws:iam::<account_id>:role/service-role/aws-quicksight-service-role-v0

[Insufficient Permissions](./troubleshooting_permission_issue.png)
