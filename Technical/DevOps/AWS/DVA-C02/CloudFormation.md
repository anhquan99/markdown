- Managing your infrastructure as code.
- CloudFormation is a declarative way of outlining your AWS Infrastructure, for any resources (most of them are supported).
# Benefits
- Infrastructure as code
	- No resources are manually created.
	- The code can be version controlled.
	- Changes to the infrastructure are reviewed through code.
- Cost:
	- Each resource within the stack is tagged with an identifier so you can easily see how much a stack costs you.
	- You can estimate the costs of your resources using the CloudFormation template.
	- Saving strategy, you can set automation to delete resource in non-working time and create resource in working time.
- Productivity
	- Ability to destroy and re-create an infrastructure on the cloud on the fly.
	- Automated generation of Diagram you your templates.
	- Declarative programming (no need to figure out ordering and orchestration).
- Separation of concern, create many stacks for many apps and many layers.
# How it works
- Templates have to be uploaded un S3 then referenced in CloudFormation.
- To update a template, you can not edit previous one, instead you have to re-upload new version of the template to AWS.
- Stack are identified by a name.
- Deleting a stack deletes every single artifact that was created by CloudFormation.
# Deploy
- Manually
	- Editing templates in the CloudFormation Designer.
	- Using the console to input parameters.
- Automatically
	- Editing templates in a YAML file.
	- Using the AWS CLI to deploy the templates.
	- Recommended way when you fully want to automate your flow.
# Building blocks
## Template components
### Resources
- AWS resources declared in the template (required).
- Identifies: `AWS::aws-product-name::data-type-name`
### Parameters
- Dynamic inputs for your template.
- Used for:
	- Reuse
	- Some inputs can not be determined ahead of time.
- Parameters are extremely powerful, controlled and can prevent errors from happening in your templates thanks to types.
- The function `Fn::Ref` can be leveraged to reference parameters, or shorthand is `!Ref`.
### Mappings
- Static variables for your template, very handy to differentiate between different envs, regions, AMI types, ...
- All the values are hardcoded with the template.
- Used for:
	- When you know in advance all the values that can be taken and that they can be deduced from variables to allow safer control over the template.
	- When the values are really user specific.
- Use with `Fn::FindlnMap` to access mapping values.
### Outputs
- References to what has been created.
- Used for referencing what you have created to another stack to perform collaboration (cross stack).
- You can not delete a CloudFormation stack if its outputs are being referenced by another CloudFormation stack.
- For other stack to import the output, use the `Fn:ImportValue` function.
### Conditionals
- List of conditions to perform resource creation to control the creation of resources or outputs based on a condition.
- Each condition can reference another condition, parameter value or mapping.
- Functions:
	- `Fn::And`
	- `Fn::Equals`
	- `Fn::If`
	- `Fn::Not`
	- `Fn::Or`
### Metadata
## Template helpers
- References
- Function
## Must know functions
- `Fn::Ref` or `!Ref`
- `Fn:GetAtt`: get attributes of the resource created
- `Fn::FindlnMap`
- `Fn::ImportValue`: import values that are exported in other templates
- `Fn::Join`: join values with a delimiter
- `Fn::Sub` or `!Sub`: substitute variables from a text
# Rollbacks
- Stack creation fails:
	- Default: everything rolls back (deleted). Write log.
	- Option to disable rollback to troubleshoot what happened.
- Stack update fails:
	- Automatically rolls back to the previous known working state.
	- Write log
# Stack notifications
- Send stack events to SNS Topic.
- Enable SNS Integration using Stack Options.
# ChangeSets
- When you update a stack, you need to know what changes before it happens for greater confidence.
- ChangeSets won't say if the update will be successful.
# Nested stacks
- Stacks as part of other stacks, allows you to isolate repeated patterns/common components in separate stacks and call them from other stacks.
- Nested stacks are considered best practice.
	- To update a nested task, always update the parent (root stack).

| Cross stacks | Nested stacks |
| --- | --- |
| Helpful when stacks have different lifecycle | Helpful when components must be re-used |
| Use output export and `Fn::ImportValue` | The nested stack only is important to the higher level stack (it's not shared) |
| When you need to pass export values to many stacks | |
# StackSets
- Create, update, or delete stacks across multiple accounts and regions with a single operation.
- Require admin to create StackSets.
- Trusted account to create, update ,delete stack instances from StackSets.
- When you update a stack set, all associated stack instances are updated throughout all accounts and regions.
# Drift
- Used to compare all the resources between versions.
# Stack policies
- Stack policies is a JSON document that defines the update actions that are allowed on specific resources during stack updates to protect resources from unintentional updates.
- Specify an explicit ALLOW for the resources you want to be allowed to be updated.