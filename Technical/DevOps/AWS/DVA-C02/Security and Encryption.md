# Key Management Service (KMS)
- Anytime you hear encryption for an AWS service, it's most likely KMS.
- AWS manages encryption keys for use.
- Fully integrated with IAM for authorization.
- Able to audit KMS Key usage using CloudTrail.
## Types
- Symmetric (AES-256)
	- You never get access to the KMS Key unencrypted (must call KMS API to use).
- Asymmetric (RSA and ECC key pairs)
	- Used for encrypt/decrypt or sign/verify operations.
- Types of LMS Keys:
	- AWS Owned Keys (free - default).
	- AWS Managed Keys (free).
	- Customer managed keys created in KMS $1 per month.
	- Customer managed keys imported (must be symmetric key) $1 per month.
- Automatic key rotation:
	- AWS-managed and customer-managed KMS Key: automatically every 1 year.
	- Imported KMS Key: only manual rotation possible using alias.
## Policies 
- Default KMS Key policy:
	- Create if you don't provide a specific KMS Key Policy.
	- Complete access to the key to the root user.
- Custom KMS Key Policy:
	- Define users, roles that can access the KMS key.
	- Define who can administer the key.
	- Useful for cross-account access of your KMS key.
## Copy snapshots across accounts
1. Create a Snapshot, encrypted with your own KMS Key (customer managed key).
2. Attach a KMS Key Policy to authorize cross-account access.
3. Share the encrypted snapshot.
4. Create a copy of the Snapshot, encrypt it with a CMK in your account.
5. Create a volume from the snapshot.
## Envelope encryption
- KMS encrypt API call has a limit of 4 KB.
- If you want to encrypt > 4 KB, use Envelope encryption.
- The main API that will help use is the `GenerateDataKey` API.
## KMS Symmetric - API Summary
- Encrypt: encrypt up to 4 KB of data through KMS.
- `GenerateDataKey` generate a unique symmetric data key (DEK)
	- Returns a plain text copy of the data key.
	- And a copy that is encrypted under the CMK that you specify.
- `GenerateDataKeyWithoutPlaintext`
	- Generate a DEK to use at some point (not immediately).
	- DEK that is encrypted under the CMK you specify (must use Decrypt later).
- `Decrypt` - decrypt up to 4 KB of data (including Data Encryption Keys).
- `GenerateRadom` - returns a random byte string.
## KMS request quotas
- When you exceed a request quota, you get a `ThrottlingException`, to response use exponential backoff (backoff and retry).
- In cryptographic operations, they share a quota, it also includes requests made by AWS on your behalf.
## CloudHSM
- AWS provisions encryption, dedicated hardware (HSM = Hardware security module).
- Customer managed CMK.
## CW Logs encryption
- Encryption is enabled at the log group level, by associating a CMK with a log group, either when you create the log group or after it exists.
- You must use the CW Log API:
	- `associate-kms-key` if the log group already exists.
	- `create-log-group` if the log group does not exist yet.
# SSM Parameter store
- Secure storage for configuration and secrets.
- Version tracking of configuration/secrets.
- `GetParameters` or `GetParametersByPath` API.
- Policies
	- Allow to assign a TTL to a parameter to force updating or deleting sensitive data such as passwords.
	- Can assign multiple policies at a time.
# AWS Secrets Manager
- Meant for storing secrets (mostly meant for RDS integration).
- Capability for force rotation of secrets every X days.
- Automate generation of secrets on rotation.
## Multi-Region Secrets
- Replicate secrets across multiple AWS regions by keeps reading replicas in sync with the primary secret.
- Ability to promote a read replica secret to a standalone secret.
## Integration CloudFormation with RDS and Aurora
- `ManageMasterUserPassword` - create admin secret implicitly.
- RDS, Aurora will manage the secret in Secrets Manager and its rotation.

| Secrets Manager ($$$) | SSM Parameter Store ($) |
| ---- | ---- |
| Automatic rotation of secrets with AWS Lambda | Simple API |
| Lambda function is provided for RDSm Redshift, Document DB | No secret rotation (can enable rotation using Lambda triggered by EventBridge) |
| KMS encryption is mandatory | KMS encryption is optional |
| Can integration with CloudFormation | Can integration with CloudFormation |
|  | Can pull a Secrets Manager secret using the SSM Parameter Store API |
# Nitro Enclaves
- Process highly sensitive data in an isolated compute environment.
- Fully isolated virtual machines, hardened, and highly constrained.
- Help reduce the attack surface for sensitive data processing apps.