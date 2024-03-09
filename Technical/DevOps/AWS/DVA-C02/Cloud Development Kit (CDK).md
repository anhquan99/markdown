- Define your cloud infrastructure using a familiar language.
- The code is compiled into a CloudFormation template (JSON/YAML).
- Lifecycle
  ![[Pasted image 20240309205235.png]]
# Benefits
- Develop and manage your IaC.
- Define your cloud infrastructure using general programming languages.
- Deploy infrastructure through CloudFormation.
- Get started developing your application quickly with constructs.
# CDK vs SAM
- SAM:
	- Serverless focused.
	- Write your template declaratively in JSON or YAML.
	- Great for quickly getting started with Lambda.
	- Leverages CloudFormation.
- CDK:
	- All AWS services.
	- Write infra in a programming language.
	- Leverage CloudFormation.
- You can use SAM CLI to locally test your CDK apps, but you must first run `cdk synth`.
# Constructs
- CDK construct is a component that encapsulates everything CDK needs to create the final CloudFormation stack.
- Construct hub - contains additional constructs from AWS, third parties and open-source CDK community.
### Layer 1 constructs (L1)
- Can be called **CFN resources**, which represents all resources directly in CloudFormation.
- Constructs are periodically generated from **CloudFormation Resource Specification**.
- Construct names start with **Cfn**.
- You must explicitly configure all resource properties.
### Layer 2 constructs (L2)
- Represents AWS resources but with a higher level.
- Similar to L1 but with convenient defaults and boilerplate.
- Provide methods that make it simpler to work with the resource.
### Layer 3 constructs (L3)
- Can be called **patterns**, which represents multiple related resources.
- Helps you complete common tasks in AWS.
# Commands
- `cdk init app` create a new CDK project from a specified template.
- `cdk synth` synthesizes and prints the CloudFormation template.
- `cdk bootstrap` deploys the CDK toolkit staging stack.
- `cdk deploy` deploy the stacks.
- `cdk diff` view the differences of local CDK and deployed stack.
- `cdk destroy` destroy the stack.
# Bootstrapping
- The process of provisioning resources for CDK before you can deploy CDK apps into an AWS environment.
# Testing
- To test CDK apps, use CDK Assertions Module combined with popular test framework to verify specific resources, rules, conditions, parameters, ...
- Types:
	- Fine-grained Assertions.
	- Snapshot test
- To import a template:
	- `Template.fromStack(MyStack)` stack built in CDK.
	- `Template.fromString(myString)` stack build outside CDK.