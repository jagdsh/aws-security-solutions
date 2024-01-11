# Why encryption?

## Encryption in flight (TLS / SSL)

- Data is encrypted before sending and decrypted after receiving
- TLS certificates help with encryption (HTTPS)
- Encryption in flight ensures no MITM (man in the middle attack) can
happen

![Encryption in flight](./encryption_in_flight.png)

## Server-side encryption at rest

- Data is encrypted after being received by the server
- Data is decrypted before being sent
- It is stored in an encrypted form thanks to a key (usually a data key)
- The encryption / decryption keys must be managed somewhere, and
the server must have access to it

![Encryption Server Side](./encryption_server_side.png)

## Client-side encryption

- Data is encrypted by the client and never decrypted by the server
- Data will be decrypted by a receiving client
- The server should not be able to decrypt the data
- Could leverage Envelope Encryption

![Encryption Client Side](./encryption_client_side.png)

## AWS Backup

- Fully managed service
- Centrally manage and automate backups across AWS services
- No need to create custom scripts and manual processes
- Supported services:
  - Amazon EC2 / Amazon EBS
  - Amazon S3
  - Amazon RDS (all DBs engines) / Amazon Aurora / Amazon DynamoDB
  - Amazon DocumentDB / Amazon Neptune
  - Amazon EFS / Amazon FSx (Lustre & Windows File Server)
  - AWS Storage Gateway (Volume Gateway)
- Supports cross-region backups
- Supports cross-account backups
- Supports Point In Time Recovery (PITR) for supported services
- On-Demand and Scheduled backups
- Tag-based backup policies
- You create backup policies known as Backup Plans
  - Backup frequency (every 12 hours, daily, weekly, monthly, cron expression)
  - Backup window
  - Transition to Cold Storage (Never, Days, Weeks, Months, Years)
  - Retention Period (Always, Days, Weeks, Months, Years)

![AWS Backup](./aws_backup.png)

### Vault Lock

- Enforce a WORM (Write Once Read Many) state for all the backups that you store in your AWS Backup Vault
- Additional layer of defense to protect your backups against:
  - Inadvertent or malicious delete operations
  - Updates that shorten or alter retention periods
- Even the root user cannot delete backups when enabled

![BackUp Vault](./aws_backup_vault.png)

## Amazon Data Lifecycle Manager

- Automate the creation, retention, and deletion of EBS snapshots and EBS-backed AMIs
- Schedule backups, cross-account snapshot copies, delete outdated backups, …
- Uses resource tags to identify the resources (EC2 instances, EBS volumes)
- Can’t be used to manage snapshots/AMIs created outside DLM
- Can’t be used to manage instance-store backed AMIs

![Data Lifecycle Manager](./data_lifecycle_manager.png)

## Nitro Enclaves

- Process highly sensitive data in an isolated compute environment
- Personally Identifiable Information (PII), healthcare, financial, …
- Fully isolated virtual machines, hardened, and highly constrained
- Not a container, not persistent storage, no interactive access, no external networking
- Helps reduce the attack surface for sensitive data processing apps
- Cryptographic Attestation – only authorized code can be running in your Enclave
- Only Enclaves can access sensitive data (integration with KMS)
- Use cases: securing private keys, processing credit cards, secure multi-party computation

![Nitro Enclave](./nitro_enclave.png)