- Give users and identity to interact with your web or mobile application.
- Cognito User Pools (CUP):
	- Sign in functionality for app users.
	- Integrate with API Gateway and ALB.
	  ![[Pasted image 20240311074456.png]]
- Cognito Identity pools (federated identity):
	- Provide AWS credentials to users so they can access AWS resources directly.
	- Integrate with User pools as an identity provider.
	- Used when you want to authorize authenticated or anonymous users to access your AWS resources.
# Scenarios
- Authenticate with a user pool.
- Access your server-side resources with a user pool.
- Access resources with API Gateway and Lambda with user pool.
- Access AWS services with a user pool and an identity pool.
- Authenticate with a 3rd party and access AWS services with an identity pool.
- Access AWS AppSync resources with Cognito.
# CUP features
- Create a serverless db of user for your web and mobile apps.
- Simple login: username (or email) / password.
- Login sends back JWT.
- Sign-up/sign-in.
- Hosted UI.
- 
## Integrations
- Integrates with API Gateway and ALB.
## Lambda triggers
- Authentication events.
- Sign-up.
- Messages.
- Token creations.
## Hosted authentication UI
- Support an authentication UI which you can customize with a custom logo and CSS.
- For custom domain you must create ACM certificate in us-east-1.
- The custom domain must be defined the **App Integration** section.
## Adaptive authentication
- For authentication.
- Block sign-ins or require MFA if the login appears suspicious.
- Cognito examines each sing-in attempt and generates a risk score for how likely the sign-in request is to be from a malicious attacker.
- User is prompted for second MFA only when the risk is detected.
# Cognitor Identity pools
- For authorization.
- Get identities for users so they obtain temporary AWS credentials, users can then access AWS services directly or through API Gateway.
## Authentication flow
- External provider flow.
  ![[Pasted image 20240311075511.png]]
  - Developer authenticated identities flow.
    ![[Pasted image 20240311075533.png]]

# Temporary security credentials (AWS Security Token Service/ AWS STS)
- Create and provide trusted user with temporary security credentials that can control access to your resources.
- When you make a call using STS, the call must include a session token.
- Temporary credentials expire after a specified interval.
- When you use STS to make a request , your principal might include a set of tags.