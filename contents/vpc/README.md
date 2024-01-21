# Network Access Analyzer

- Helps you understand potential network paths to/from your resources
- Define Network Access Requirements
- Example: identify publicly available resources
- Evaluate against them and find issues / demonstrate compliance
- Evaluate network access to resources in your VPCs (EC2, RDS, Aurora, OpenSearch, Redshift…)
- Match against the configurations of your VPC resources (SG, NACL, NATGW, IGW…)
- Network Access Scope – json document contains conditions to define your network security policy (e.g., detect public databases)

![Network Access Analyzer](./network_analyzer.png)

## Summary

- Verify network access compliance.
- Scopes define conditions for paths of interest, and can exclude compliant paths.
- Analysis findings show paths of interest.
- All NICs in a region are analyzed.
