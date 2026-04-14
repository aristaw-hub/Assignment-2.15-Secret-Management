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



## 3. Sensible ARN for secret name `prod/cart-service/credentials`

Assuming:
- **AWS Region:** `us-east-1`
- **AWS Account ID:** `123456789012`

The full ARN would be:

```
arn:aws:secretsmanager:us-east-1:123456789012:secret:prod/cart-service/credentials-??????
```

**For the IAM policy `Resource` field, you should use:**

```
arn:aws:secretsmanager:us-east-1:123456789012:secret:prod/cart-service/credentials-*
```

**Best Practice:** Replace `<aws-region>` and `<aws-account-id>` with your actual values, and keep the wildcard `-*` to match the automatically generated suffix.

## Summary Table

| Requirement.                 | Value.                                 |
|-------------                 |-------                                 |
| **Authorization mechanism**  | IAM Role attached via Instance Profile |
| **Action needed**            | `secretsmanager:GetSecretValue`.       |
| **Secret name**.             | `prod/cart-service/credentials`.       |
| **Example ARN (with wildcard)** | `arn:aws:secretsmanager:us-east-1:123456789012:secret:prod/cart-service/credentials-*` |
| **IAM Policy Effect**.       | `Allow`                                |

## Acknowledgments

- Reference: AWS Documentation on [Permissions for Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/auth-and-access.html)
- Class discussion reference: NTU Cloud Computing / DevOps module

---
*Submission for NTU Blackboard - In-Class/Zoom Activity*
```

---

## What you need to do manually (submission steps)

1. **Create a public GitHub repository** (e.g., `aws-ec2-secretsmanager-activity`).
2. **Create a `README.md` file** inside it and paste the above content.
3. **Copy the GitHub repository link**.
4. **Upload to NTU Blackboard**:
   - Either paste the GitHub link directly into the submission text box.
   - Or create a **PDF/Word document** containing the README content plus the GitHub link, and upload that file.
5. **Optional Presentation:** If you need a PowerPoint/PDF slide deck, create 3 slides (Question, Policy JSON, ARN example) using the content above.

---

## If a different task was discussed during Zoom

If your instructor changed the activity (e.g., using KMS encryption context, or VPC endpoints for Secrets Manager), please reply with the alternate task and I will regenerate the answer accordingly.
