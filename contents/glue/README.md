# AWS Glue

- Managed extract, transform, and load (ETL) service
- Useful to prepare and transform data for analytics
- Fully serverless service

![AWS Glue](./glue.png)

## Convert data into Parquet format

![Convert into Parquet](./covert_into_parquet.png)

## Glue Data Catalog: catalog of datasets

![Glue Catalog](./glue_catalog.png)

## Things to know at a high-level

**[Troubleshooting]**:

- Glue Job Bookmarks: prevent re-processing old data
- Glue Elastic Views:
  - Combine and replicate data across multiple data stores using SQL
  - No custom code, Glue monitors for changes in the source data, serverless
  - Leverages a “virtual table” (materialized view)
- Glue DataBrew: clean and normalize data using pre-built transformation
- Glue Studio: new GUI to create, run and monitor ETL jobs in Glue
- Glue Streaming ETL (built on Apache Spark Structured Streaming):
  compatible with Kinesis Data Streaming, Kafka, MSK (managed Kafka)

## Security

- Encryption at rest using KMS for databases, tables, Job Bookmarks,
- Encryption in transit using TLS
- Identity-Based Policies – grant access to Glue resources by attaching policies to IAM identities (user, group, role, service)
- Resource-Based Policies – policy attached to Glue Data Catalog to grant access to IAM identities (used for cross-account access)

**Identity-Based Policy:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "glue: GetTable",
        "glue: GetTables",
        "glue: GetDatabase",
        "glue: GetDatabases"
      ],
      "Resource": [
        "arn:aws:glue:us-west-2:123456789012: catalog",
        "arn: aws: glue:us-west-2:123456789012:database/db1",
        "arn:aws: glue:us-west-2:123456789012: table/db1/books"
      ]
    }
  ]
}
```

## Centralized Data Catalog

**Glue Data Catalog Policy:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws: iam:: 444455556666: root"
      },
      "Action": ["glue:GetDatabases*", "glue:GetTables*"],
      "Resource": [
        "arn: aws:glue:us-east-1:111122223333:catalog",
        "arn:aws: glue:us-east-1:111122223333:database/producer_database",
        "arn:aws:glue:us-east-1:111122223333: table/producer_database/orders"
      ]
    }
  ]
}
```

**S3 bucket Policy:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws: iam::444455556666: root"
      },
      "Action": ["s3: GetObject", "s3:ListBucket"],
      "Resource": [
        "arn:aws: s3: ::my-products-bucket",
        "arn:aws: s3: ::my-products-bucket/orders/*"
      ]
    }
  ]
}
```

![Centralzed Data Catalog](./centralized_data_catalog.png)

