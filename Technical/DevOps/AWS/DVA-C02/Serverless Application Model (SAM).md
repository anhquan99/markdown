- Framework for developing and deploying serverless applications.
- All the configuration is YAML code.
- Generate complex CloudFormation from SAM YAML file.
# Recipe
- Transform header indicates: `Transform: 'AWS::Serverless-2016-10-31'`.
- Write code:
	- `AWS::Serverless::Function`
	- `AWS::Serverless:API`
	- `AWS::Serverless:SimpleTable`
- Pack and deploy:
	- `aws cloudformation package / sam package`
	- `aws cloudformation deploy / sam deploy`
# Local capabilities
- Locally start AWS Lambda.
- Locally invoke Lambda function.
- Locally Start an API Gateway Endpoint.
- Generate AWS Events for Lambda functions.