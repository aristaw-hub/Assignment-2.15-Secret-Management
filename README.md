# Assignment-2.15-Secret-Management

# AWS EC2 to Secrets Manager IAM Configuration and Authorization Activity

This document answers the in-class/Zoom activity questions regarding authorizing an EC2 instance to retrieve secrets from AWS Secrets Manager.

---

## Question 1: What is needed to authorize your EC2 to retrieve secrets from AWS Secret Manager?

To authorize an EC2 instance to retrieve secrets from AWS Secrets Manager, the following components are required:

### Prerequisites & Components:

| # | Component | Purpose |
|---|-----------|---------|
| 1 | **IAM Role** | Create an IAM role (not an IAM user) that will be assumed by the EC2 instance |
| 2 | **Trust Policy** | Attach a trust policy allowing `ec2.amazonaws.com` to assume the role |
| 3 | **IAM Permissions Policy** | Attach a policy granting `secretsmanager:GetSecretValue` permission |
| 4 | **Instance Profile** | Create an instance profile and attach the IAM role to it |
| 5 | **EC2 Attachment** | Launch the EC2 instance with the instance profile attached |
| 6 | **AWS SDK/CLI** | Application must use AWS SDK or CLI that automatically retrieves temporary credentials from instance metadata |

### Step-by-Step Process:

1. **Create IAM Role** via AWS Console, CLI, or CloudFormation
2. **Define Trust Relationship** to allow EC2 service to assume the role
3. **Attach Permission Policy** with `secretsmanager:GetSecretValue` action
4. **Attach role to EC2** at launch (or to an existing instance via "Attach/Replace IAM Role")
5. **Application code** calls `GetSecretValue` API using default credential chain

---

## Question 2: Derive the IAM policy (i.e., JSON)

Below is the least-privilege IAM policy JSON that should be attached to the EC2 IAM role:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowEC2ToRetrieveSecret",
            "Effect": "Allow",
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "arn:aws:secretsmanager:us-east-1:123456789012:secret:prod/cart-service/credentials-*"
        }
    ]
}
