# Cognito

## Cognito User Pools (CUP) – User Features

- Create a serverless database of user for your web & mobile apps
- Simple login: Username (or email) / password combination
- Password reset
- Email & Phone Number Verification
- Multi-factor authentication (MFA)
- Federated Identities: users from Facebook, Google, SAML…
- Feature: block users if their credentials are compromised elsewhere
- Login sends back a JSON Web Token (JWT)

## Cognito User Pools (CUP) – Diagram

![Cognito User Pools (CUP)](./cognito_user_pool.png)

## Cognito User Pools (CUP) - Integrations

- CUP integrates with API Gateway and Application Load Balancer

![CUP Integrations](./cup_integrations.png)

## Cognito User Pools – Lambda Triggers

- CUP can invoke a Lambda function synchronously on these triggers:

| User Pool Flow        |              Operation              |                                                   Description |
| --------------------- | :---------------------------------: | ------------------------------------------------------------: |
| Authentication Events |  Pre Authentication Lambda Trigger  |       Custom validation to accept or deny the sign-in request |
| ^^                    | Post Authentication Lambda Trigger  |                            Event logging for custom analytics |
| ^^                    | Pre Token Generation Lambda Trigger |                              Augment or suppress token claims |
| Sign-Up               |     Pre Sign-up Lambda Trigger      |       Custom validation to accept or deny the sign-up request |
| ^^                    |  Post Confirmation Lambda Trigger   | Custom welcome messages or event logging for custom analytics |
| ^^                    |     Migrate User Lambda Trigger     |  Migrate a user from an existing user directory to user pools |
| Messages              |    Custom Message Lambda Trigger    |           Advanced customization and localization of messages |
| Token Creation        | Pre Token Generation Lambda Trigger |                         Add or remove attributes in Id tokens |

[Reference](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools-working-with-aws-lambda-triggers.html)

## Cognito User Pools – Hosted Authentication UI

- Cognito has a hosted authentication UI that you can add to your app to handle sign-up and sign-in workflows
- Using the hosted UI, you have a foundation for integration with social logins, OIDC or SAML
- Can customize with a custom logo and custom CSS

![Host Authenticated UI](./hosted_authentication_ui.png)

[Reference](https://aws.amazon.com/blogs/aws/launch-amazon-cognito-user-pools-general-availability-app-integration-and-federation/)

## Cognito Identity Pools

### (Federated Identities)

- Get identities for “users” so they obtain temporary AWS credentials
- Your identity pool (e.g identity source) can include:
  - Public Providers (Login with Amazon, Facebook, Google, Apple)
  - Users in an Amazon Cognito user pool
  - OpenID Connect Providers & SAML Identity Providers
  - Developer Authenticated Identities (custom login server)
  - Cognito Identity Pools allow for unauthenticated (guest) access
- Users can then access AWS services directly or through API Gateway
  - The IAM policies applied to the credentials are defined in Cognito
  - They can be customized based on the user_id for fine grained control

## Cognito Identity Pools – Diagram

![Cognito Identity Pool](./cognito_identity_pool.png)

- The user wants to access the S3 and DynamoDB but we don't want to create IAM Users.

## Cognito Identity Pools – Diagram with CUP

![Cognito Identity Pool CUP](./cognito_identity_pool_cup.png)

## Cognito Identity Pools – IAM Roles

- Default IAM roles for authenticated and guest users
- Define rules to choose the role for each user based on the user’s ID
- You can partition your users’ access using policy variables
- IAM credentials are obtained by Cognito Identity Pools through STS
- The roles must have a “trust” policy of Cognito Identity Pools

## Cognito Identity Pools – Guest User example

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": ["s3:GetObject"],
      "Effect": "Allow",
      "Resource": ["arn:aws: s3:::mybucket/assets/my_picture.jpg"]
    }
  ]
}
```

## Cognito Identity Pools – Policy variable on S3

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": ["s3:ListBucket"],
      "Effect": "Allow",
      "Resource": ["arn:aws:53:::mybucket"],
      "Condition": {
        "StringLike": {
          "S3:prefix": ["${cognito-identity.amazonaws.com:sub}/*"]
        }
      }
    },
    {
      "Action": ["s3: GetObject", "s3:Putobject"],
      "Effect": "Allow",
      "Resource": [
        "arn: aws:53:::mybucket.${cognito-identity.amazonaws.com:sub}/*"
      ]
    }
  ]
}
```

## Cognito Identity Pools – DynamoDB

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:BatchGetItem",
        "dynamodb:Query",
        "dynamodb:PutItem",
        "dynamodb:UpdateIten",
        "dynamodb:Deleteltem",
        "dynamodb:BatchWriteltem"
      ],
      "Resource": ["arn:aws:dynamodb:us-west-2: 123456789012:table/MyTable"],
      "Condition": {
        "ForAllValues:StringEquals": {
          "dynamodb: LeadingKeys": ["${cognito-identity.amazonaws.com:sub}"]
        }
      }
    }
  ]
}
```

## Cognito User Pools vs Identity Pools

- Cognito User Pools:
  - Database of users for your web and mobile application
  - Allows to federate logins through Public Social, OIDC, SAML…
  - Can customize the hosted UI for authentication (including the logo)]
  - Has triggers with AWS Lambda during the authentication flow
- Cognito Identity Pools:
  - Obtain AWS credentials for your users
  - Users can login through Public Social, OIDC, SAML & Cognito User Pools
  - Users can be unauthenticated (guests)
  - Users are mapped to IAM roles & policies, can leverage policy variables
- CUP + CIP = manage user / password + access AWS services

## Cognito Sync

- Deprecated – use AWS AppSync now
- Store preferences, configuration, state of app
- Cross device synchronization (any platform – iOS, Android, etc…)
- Offline capability (synchronization when back online)
- Store data in datasets (up to 1MB), up to 20 datasets to synchronize
- Push Sync: silently notify across all devices when identity data changes
- Cognito Stream: stream data from Cognito into Kinesis
- Cognito Events: execute Lambda functions in response to events

## Cognito User Pool Groups

- Collection of users in a logical group in Cognito User Pool
- Defines the permissions for users in the group by assigning IAM role to the group
- Users can be in multiple groups:
- Assign precedence values to each group (lower will be chosen and its IAM role will be applied)
- Choose from available IAM roles by specifing the IAM role ARN
- You can’t create nested groups

![Cognito User Pool Group](./cognito_user_pool_group.png)