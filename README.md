# Assignment-2.15-Secret-Management

# AWS EC2 to Secrets Manager IAM Configuration

This document answers the in-class activity regarding authorizing an EC2 instance to retrieve secrets from AWS Secrets Manager.

## 1. What is needed to authorize your EC2 to retrieve secrets from AWS Secrets Manager?

To authorize an EC2 instance to retrieve secrets, you need the following components:

1.  **An IAM Role:** Create an IAM Role (not a User) that the EC2 instance will assume.
2.  **Trust Policy (Assume Role):** The Role must have a *Trust Policy* that allows the `ec2.amazonaws.com` service principal to assume the role.
3.  **IAM Policy (Permissions):** The Role must have a *Permissions Policy* that grants the necessary actions (`secretsmanager:GetSecretValue`) on the specific secret resource.
4.  **Instance Profile:** The IAM Role must be attached to an Instance Profile, which is then attached to the EC2 instance at launch (or to a running instance).
5.  **SDK/CLI Configuration:** The application running on EC2 must use the AWS SDK or CLI, which automatically retrieves temporary credentials from the instance metadata service.

## 2. IAM Policy (JSON)

Below is the least-privilege IAM policy required. It allows the EC2 instance to retrieve the secret value but not modify or delete it.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "RetrieveSecretFromSecretsManager",
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "arn:aws:secretsmanager:<aws-region>:<aws-account-id>:secret:prod/cart-service/credentials-*"
        }
    ]
}
