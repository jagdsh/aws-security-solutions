# Amazon Simple Email Service (Amazon SES)

- Fully managed service to send emails securely, globally and at scale
- Allows inbound/outbound emails
- Reputation dashboard, performance insights, anti-spam feedback
- Provides statistics such as email deliveries, bounces, feedback loop results, email open
- Supports DomainKeys Identified Mail (DKIM) and Sender Policy Framework (SPF)
- Flexible IP deployment: shared, dedicated, and customer-owned IPs
- Send emails using your application using AWS Console, APIs, or SMTP
- Use cases: transactional, marketing and bulk email communications

![Simple Email Service](./ses.png)

## Configuration Sets

- Configuration sets help you customize and analyze your email send events
- Event destinations:
- Kinesis Data Firehose: receives metrics (numbers of sends, deliveries, opens, clicks, bounces, and complaints) for each email
- SNS: for immediate feedback on bounce and complaint information
- IP pool management: use IP pools to send particular types of emails

![Configuration Sets](./configuration_sets.png)
