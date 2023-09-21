# What is Secret Manager? 
- Managed AWS service that protects API keys, tokens, database, creds..
- Centralize secret storage and encrypts data
- Access is managed through IAM and monitored via CloudWatch
- Allows you to control the lifecycle of secrets including rotation
- 30 Day of Free Trial Period
- 0.40$ per secret per month
- 0.05$ per 10,000 GetSecretValue API call
- However, secrets are usually highly cache-able
- If no key rotation id is provided, then Secrets Manager defaults to using the AWS account's default KMS key named `aws/secretsmanager`
- When a secret is deleted from secret manager, the default recovery time is `30 days`. Which means that AWS secret manager waits for 30 days before deleting the secret. This value can be changed from `0 to 30 days`.
# Lambda Retrieving Secret
See Documentation here [SecretsManager Layer](./lambda_layers/readme.md)