# Users and groups
- It's a global service
- Root account created by default, shouldn't be used or shared.
- Group only contain users, not other groups.
# Permissions
- Users or groups can be assigned JSON documents called policies.
- These policies define the permissions of the users.
# Policies
## Structure
- Version: policy language version.
- ID: an identifier for the policy (optional).
- Statement: one or more individual statements (required)
	- Sid: an identifier for the statement (optional).
	- Effect allows or denies.
	- Principal: account/user/role to which this policy applied to.
	- Resource: list of resources to which the actions applied to.
	- Condition: for when this policy is in effect (optional)
# Access AWS
- Management Console: password + MFA.
- CLI: access keys.
- SDK: access keys.
# IAM Roles for Services
- Some AWS service will need to perform actions on your behalf, you need to assign permissions to AWS services with IAM Roles.
# IAM Security Tools
- IAM Credentials Report (account-level): list all your account's users and the status of their various credentials.
- IAM Access Advisor (user-level): show the service permissions granted to a user and when those services were last accessed.
# Guidelines & Best Practices
- Don't use the root account except for AWS account setup.
- Per AWS account per user.
- Assign users to group and assign permissions to groups.
- Create and use Roles for giving permissions to AWS services.
- Use Access Keys for Programmatic Access (CLI/SDK)
- Audit permissions.
- Never share IAM users and Access Keys