# Identity-based policies
- JSON permissions policy documents that control what actions an identity (users, groups of users, and roles) can perform on which resource, and under what conditions.
## Category
- <mark style="background: #FF5582A6;">Managed policies:</mark> standalone identity-based that you can attach to multiple users, groups and roles.
	- AWS managed policies.
	- Customer managed policies: users create policies.
- <mark style="background: #FF5582A6;">Inline policies:</mark> add directly to a single user, group, or role.
# Resource-based policies
- JSON policy documents that you attach to a resource to grant the specified principal permission to perform specific action on that resource under what condition this applies.
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
- <mark style="background: #FFB86CA6;">IAM Credentials Report (account-level):</mark> list all your account's users and the status of their various credentials.
- <mark style="background: #FFB86CA6;">IAM Access Advisor (user-level):</mark> show the service permissions granted to a user and when those services were last accessed.
# Guidelines & Best Practices
- Don't use the root account except for AWS account setup.
- Per AWS account per user.
- Assign users to group and assign permissions to groups.
- Create and use Roles for giving permissions to AWS services.
- Use Access Keys for Programmatic Access (CLI/SDK)
- Audit permissions.
- Never share IAM users and Access Keys