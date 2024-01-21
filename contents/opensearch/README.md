# OpenSearch Service

- Amazon OpenSearch is successor to Amazon ElasticSearch
- In DynamoDB, queries only exist by primary key or indexes…
- With OpenSearch, you can search any field, even partially matches
- It’s common to use OpenSearch as a complement to another database
- Two modes: managed cluster or serverless cluster
- Does not natively support SQL (can be enabled via a plugin)
- Ingestion from Kinesis Data Firehose, AWS IoT, and CloudWatch Logs
- Security through Cognito & IAM, KMS encryption, TLS
- Comes with OpenSearch Dashboards (visualization)

## Open Search Patterns DynamoDb

- Near Real time
- Application deployed in M5
- We retrieve the original items in DynamoDB table.

![Open Search](./opensearch.png)

## Cloud watch into Open Search

![Cloudwatch injestion](./opensearch_cloudwatch.png)

## Kinesis Data Stream & Kinesis Data Firehouse into Opensearch

![Kinesis into Opensearch](./opensearch_kinesis.png)

## Public Access

- Accessible from the Internet with a public endpoint
- Restrict access using Access Policies, Identity-based Policies, and IP-based Policies

![Public Access](./opensearch_public_access.png)

## OpenSearch VPC access

- Specify VPC, Subnets, Security Groups, and IAM Role
- VPC Endpoints and ENIs will be created (IAM Role)
- You need to use VPN, Transit Gateway, managed network, or proxy server to connect to the domain
- Restrict access using Access Policies and Identity-based Policies

![OpenSearh is only accesible to VPC networks](./opensearch_vpc_access.png)

### OpenSearch - Deploy in VPC

- Domain Access Policy – specify which actions a principal can perform on the domains subresources (e.g., indexes, APIs)

- Grant IAM user full access on all the OpenSearch domain sub-resources

![OpenSearch Full Access](./opensearch_vpc_full_access_iam.png)

- Grant IAM role access on some methods to access the OpenSearch domain sub-resources

![OpenSearch Restricted Access](./opensearch_vpc_restricted_access_iam.png)

#### Limitations of VPC deployments

- You can’t switch from VPC to a public endpoint. The reverse is also true
- You can’t launch your domain within a VPC that uses dedicated tenancy
- After you place a domain within a VPC, you can’t move it to a different VPC,but you can change the subnets and security group settings
- Cannot use IP-based access policies