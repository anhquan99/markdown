- Framework for developing and deploying serverless applications.
- All the configuration is YAML code.
- Generate complex CloudFormation from SAM YAML file.
# Specification
- Build on [[CloudFormation]].
- An extension of CloudFormation.
- An abstract, short-hand syntax.
- Transformational.
## Anatomy
```yaml
Transform: AWS::Serverless-2016-10-31

Globals:
  set of globals

Description:
  String

Metadata:
  template metadata

Parameters:
  set of parameters

Mappings:
  set of mappings

Conditions:
  set of conditions

Resources:
  set of resources

Outputs:
  set of outputs

```
- Transform is required to identify an AWS CloudFormation template.
- Global is optional to defines properties that are common to all serverless function.
- Resource is required, contains a combination of CloudFormation and SAM resources.
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
```ad-note
- Which AWS service allows you to share your Serverless applications packages using SAM with other AWS accounts?
	- AWS Serverless Application Repository (AWS SAR)
```