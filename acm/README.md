# AWS Certificate Manager (ACM)

- To host public SSL certificates in AWS, you can:
  - Buy your own and upload them using the CLI
  - Have ACM provision and renew public SSL certificates for you (free of cost)
- ACM loads SSL certificates on the following
integrations:
  - Load Balancers (including the ones created by EB)
  - CloudFront distributions
  - APIs on API Gateways
- SSL certificates is overall a pain to manually
manage, so ACM is great to leverage in your
AWS infrastructure!

![ACM](./acm.png)

## ACM – Good to know

- Possibility of creating public certificates
  - Must verify public DNS
  - Must be issued by a trusted public certificate authority (CA)
- Possibility of creating private certificates
  - For your internal applications
  - You create your own private CA
  - Your applications must trust your private CA
- Certificate renewal:
  - Automatically done if generated provisioned by ACM
  - Any manually uploaded certificates must be renewed manually and re-uploaded
- ACM is a regional service
  - To use with a global application (multiple ALB for example), you need to issue an SSL certificate in each region where you application is deployed.
  - You cannot copy certs across regions

## AWS Private Certificate Authority (CA)

- Managed service allows you to create private Certificate Authorities (CA), including root and subordinaries CAs
- Can issue and deploy end-entity X.509 certificates
- Certificates are trusted only by your Organization (not the public Internet)
- Integrates with Amazon EKS with and any AWS
service that is integrated with ACM
- Use cases:
  - Encrypted TLS communication, Cryptographically signing code
  - Authenticate users, computers, API endpoints, and IoT devices
  - Enterprise customers building a Public Key Infrastructure (PKI)

![AWS CA](./aws_ca.png)

### Validation Techniques

- Before ACM issue a public certificate, you must prove
that you own/control the domain
- DNS Validation (recommended)
  - Leverages a CNAME record created in DNS config (e.g.,
Route 53)
  - Preferred for automatic renewal purposes
  - Takes a few minutes to verify
- Email Validation
  - a Validation Email is sent to contact addresses in the WHOIS
database
  - Takes a few minutes to verify
- Note: ACM Validation is NOT required for imported
certificates or certificates signed by a Private CA

| Type | Name | Value |
| ---- | ---- | ---- |
|CNAME | _a79865eb4cd1a6ab990a45779b4e0b96.example.com. | _424c7224e9b0146f9a8808af955727d0.acm-validations.aws.com |

![Validation Technique](./validation_technique.png)

### Automatic Renewal

- ACM Fails to Renew a DNS Validated Certificate
  - Most likely due to missing or inaccurate CNAME records in your DNS config.
  - You can try Email Validation (requires action by the Domain owner)
- ACM sends renewal notices 45 days before expiration
- Renewal emails sent to the Domain’s WHOIS mailbox addresses
- Email contains a link that Domain owner can click for easy renewal
- ACM issues a renewed certificate with the same ARN

![Automatic Renewal](./automatic_renewal.png)

## Pending Validation – How to resolve?

- Resolution:
  - Confirm CNAME record is added to the correct DNS config.
  - Confirm CNAME record in your DNS config. contains no additional characters or has no missing characters
  - If your DNS Provider automatically adds the bare domain to the end of its DNS records, remove the bare domain from the DNS record name
  - _a79865eb4cd1a6ab990a45779b4e0b96.example.com.example.com
  - If there’re both CNAME and TXT records for the same domain name, then delete the TXT record

```bash
$ dia +short CNAME a79865eb4cd1a6ab990a45779b4e0b96.example.com.

$ dig TXT _a79865eb4cd1a6ab990a45779b4e0b96. example.com.
```

## Process to Manually Create a Certificate

- You can create a Certificate manually, then upload the Certificate to either ACM or IAM

![Manually Create Certificate](./manually_create_certificate.png)

### Monitor Expired Imported Certificates

- ACM sends daily expiration events starting 45 days prior to expiration
  - The # of days can be configured
  - Events are appearing in EventBridge
- AWS Config has a managed rule named acm-certificate-expiration-check to check for expiring certificates (configurable number of days) 

![Monitor Imported Certificates](./acm_monitor_cert.png)

