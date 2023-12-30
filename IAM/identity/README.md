# Identity

## Identity Federation in AWS

- Give users outside of AWS permissions to access AWS resources in your account
- You don’t need to create IAM Users (user management is outside AWS)
- Use cases:
  - A corporate has its own identity system (e.g., Active Directory)
  - Web/Mobile application that needs access to AWS resources
- Identity Federation can have many flavors:
  - SAML 2.0
  - Custom Identity Broker
  - Web Identity Federation With(out) Amazon Cognito
  - Single Sign-On (SSO)

![Identity Federation AWS](./identity_federation_aws.png)

## SAML 2.0 Federation

- Security Assertion Markup Language 2.0 (SAML 2.0)
- Open standard used by many identity providers (e.g., ADFS)
  - Supports integration with Microsoft Active Directory Federations Services (ADFS)
  - Or any SAML 2.0–compatible IdPs with AWS
- Access to AWS Console, AWS CLI, or AWS API using temporary credentials
  - No need to create IAM Users for each of your employees
  - Need to setup a trust between AWS IAM and SAML 2.0 Identity Provider (both ways)
- Under-the-hood: Uses the STS API AssumeRoleWithSAML
- SAML 2.0 Federation is the “old way”, Amazon Single Sign-On (AWS SSO) Federation is the new managed and simpler way
  - [SAML 2.0 Federation](https://aws.amazon.com/blogs/security/enabling-federation-to-aws-using-windows-active-directory-adfs-and-saml-2-0/)

## SAML 2.0 Federation – AWS API Access

![SAML 2.0 Federation AWS API Access](./saml_federation_api_access.png)

## SAML 2.0 Federation – AWS Console Access

![SAML 2.0 Federation AWS Console Access](./saml_federation_console_access.png)

## SAML 2.0 Federation – Active Directory FS (ADFS)

![SAML 2.0 Federation Active Directory Access](./saml_federation_ad_access.png)

## Custom Identity Broker Application

- Use only if Identity Provider is NOT compatible with SAML 2.0
- The Identity Broker Authenticates users & requests temporary credentials from AWS
- The Identity Broker must determine the appropriate IAM Role
- Uses the STS API AssumeRole or GetFederationToken

![Custom Identity Broker Application](./customer_identity.png)

## Web Identity Federation – Without Cognito

- Not recommended by AWS – use Cognito instead

![Web Identity without Cognito](./web_identity_without_congnito.png)

## Web Identity Federation – With Cognito

- Preferred over for Web Identity Federation
  - Create IAM Roles using Cognito with the least privilege needed
  - Build trust between the OIDC IdP and AWS
- Cognito benefits:
  - Supports anonymous users
  - Supports MFA
  - Data Synchronization
- Cognito replaces a Token Vending Machine (TVM)

![Web Identity with Cognito](./web_identity_with_congnito.png)

## Web Identity Federation – IAM Policy

- After being authenticated with Web Identity Federation, you can identify the user with an IAM policy variable
- Examples:
  - cognito-identity.amazonaws.com:sub
  - www.amazon.com:user_id
  - graph.facebook.com:id
  - accounts.google.com:sub

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "33:ListBucket",
      "Resource": "arn:aws:s3::: nyBucket",
      "Condition": {
        "StringLike": {
          "s3: prefix": "Anazon/mynumbersgane/S(www.amazon.com:user_1d}/*"
        }
      }
    },
    {
      "Effect": "Allow",
      "Action": ["53:Getobject", "33: Putobject", "53: DeleteObject"],
      "Resource": [
        "arn:aws:53: ::myBucket/anazon/mynunbersgane/$(www.anazon.consuser_id)",
        "arn:aws: 53:::myBucket/anazon/mynunbersgane/S(www.amazon.con:user_id}/**"
      ]
    }
  ]
}
```

## Configuring SAML 2.0-based Federation

![SAML Federation Configuration](./saml_federation_configuration.png)

## Example SAML 2.0 IdP Metadata (XML) File

![SAML IDP XML File](./saml_idp_xml.png)

## SAML 2.0 Federation – Troubleshooting

- Error : Response signature invalid (service: AWSSecurityTokenService; status code: 400; error code: InvalidIdentityToken)
- Reason: federation metadata of the identity provider does NOT match the metadata of the IAM identity provider
  - Example: metadata file might have changed to update an expired certificate
- Resolution:
  - Download the updated SAML 2.0 metadata file from the identity provider
  - Update in the IAM identity provider using AWS CLI aws iam update-saml-provider

## AWS IAM Identity Center (successor to AWS Single Sign-On)

- One login (single sign-on) for all your
  - AWS accounts in AWS Organizations
  - Business cloud applications (e.g., Salesforce, Box, Microsoft 365, …)
  - SAML2.0-enabled applications
  - EC2 Windows Instances
- Identity providers
  - Built-in identity store in IAM Identity Center
  - 3rd party: Active Directory (AD), OneLogin, Okta

## AWS IAM Identity Center – Login Flow

![AWS IAM Identity Center](./aws_iam_identity_center_login.png)

## AWS IAM Identity Center

![AWS IAM Identity Center](./aws_iam_identity_center.png)

## IAM Identity Center

![IAM Identity Center](./iam_identity_center.png)

## AWS IAM Identity Center Fine-grained Permissions and Assignments

- Multi-Account Permissions
  - Manage access across AWS accounts in your AWS Organization
  - Permission Sets – a collection of one or more IAM Policies assigned to users and groups to define AWS access
- Application Assignments
  - SSO access to many SAML 2.0 business applications (Salesforce, Box, Microsoft 365, …)
  - Provide required URLs, certificates, and metadata
- Attribute-Based Access Control (ABAC)
  - Fine-grained permissions based on users’ attributes stored in IAM Identity Center Identity Store
  - Example: cost center, title, locale, …
  - Use case: Define permissions once, then modify AWS access by changing the attributes

![IAM Identity Center Permissions and Assignments](./iam_identity_center_permissions_assignment.png)
