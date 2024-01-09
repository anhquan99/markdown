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