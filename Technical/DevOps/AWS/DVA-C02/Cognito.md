- Give users and identity to interact with your web or mobile application.
- Cognito User Pools (CUP):
	- Sign in functionality for app users.
	- Integrate with API Gateway and ALB.
- Cognito Identity pools (federated identity):
	- Provide AWS credentials to users so they can access AWS resources directly.
	- Integrate with User pools as an identity provider.
# CUP features
- Create a serverless db of user for your web and mobile apps.
- Simple login: username (or email) / password.
- Login sends back JWT.
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
