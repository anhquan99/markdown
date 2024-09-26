*HashiCorp Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share.*
- The Terraform language is declarative, describing an intended goal rather than the steps to reach that goal. The ordering of blocks and the files they are organized into are generally not significant; Terraform only considers implicit and explicit relationships between resources when determining an order of operations.
# Backend
- A backend defines where Terraform stores its state data files.
- Terraform uses persisted state data to keep track of the resources it manages.
# State
- Terraform must store state about your managed infrastructure and configuration. This state is used by Terraform to map real world resources to your configuration, keep track of metadata, and to improve performance for large infrastructures.
# Workspace
- Local
- Cloud
# Modules
- With Terraform, you can put your code inside a Terraform module and reuse that module in multiple places throughout your code. Instead of having the same code copied and pasted in the staging and production environments.
![[Pasted image 20240902150857.png]]
# Workflow
![[Pasted image 20240902145104.png]]
# Syntax
```terraform
resource "aws_vpc" "main" {
  cidr_block = var.base_cidr_block
}

<BLOCK TYPE> "<BLOCK LABEL>" "<BLOCK LABEL>" {
  # Block body
  <IDENTIFIER> = <EXPRESSION> # Argument
}

```
# Conventions
## Files
- A `backend.tf` file that contains your [backend configuration](https://developer.hashicorp.com/terraform/language/backend). You can define multiple `terraform` blocks in your configuration to separate your backend configuration from your Terraform and provider versioning configuration.
- A `main.tf` file that contains all resource and data source blocks.
- A `outputs.tf` file that contains all output blocks in alphabetical order.
- A `providers.tf` file that contains all `provider` blocks and configuration.
- A `terraform.tf` file that contains a single `terraform` block which defines your `required_version` and `required_providers`.
- A `variables.tf` file that contains all variable blocks in alphabetical order.
- A `locals.tf` file that contains local values. Refer to [local values](https://developer.hashicorp.com/terraform/language/style#local-values) for more information.
- A `override.tf` file that contains override definitions for your configuration. Terraform loads this and all files ending with `_override.tf` last. Use them sparingly and add comments to the original resource definitions, as these overrides make your code harder to reason about. Refer to the [override files](https://developer.hashicorp.com/terraform/language/files/override) documentation for more information.
## Resource naming
- Unique name and do not include  
- Use with underscores
- Wrap the resource type and the name in double quotes
```terraform
# bad
resource aws_instance webAPI-aws-instance {...}

# good
resource "aws_instance" "web_api" {...}
```
## Repository structure
- Store infrastructure configuration separately from module code
- Store each module in an individual repository
- Organize infrastructure configuration in repositories that group together logically-related resources. Examples: web app, networking and database.
- Monolithic: simple but complicated CI/CD automation
```terraform
.
├── modules
│   ├── function
│   │   ├── main.tf      # contains aws_iam_role, aws_lambda_function
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── queue
│   │   ├── main.tf      # contains aws_sqs_queue
│   │   ├── outputs.tf
│   │   └── variables.tf
│   └── vpc
│       ├── main.tf      # contains aws_vpc, aws_subnet
│       ├── outputs.tf
│       └── variables.tf
├── main.tf
├── outputs.tf
└── variables.tf
```
# Syntax - Blocks
```terraform
resource "aws_instance" "example" {
  ami = "abc123"

  network_interface {
    # ...
  }
}
```
- A block has a type (`resource` in this example). Each block type defines how many `labels` must follow the type keyword.
- The `resource` block type expects 2 labels, which are `aws_instance` and `example` in the example. The `aws_instance` label is specific to the AWS provider which specifies the `resource` type that Terraform provisions when you apply the configuration. The second label is an arbitrary name.
- You can create multiple instances of the same block type and differentiate them by giving each instance a unique name.
# Resources
# Providers
- A provider is a plugin for Terraform that offers a collection of resource types.
## Meta-arguments
- Used with any resource type to change the behavior of resources:
	- `depends_on`: specifying hidden dependencies
	- `count`: create multiple resource according to a count
	- `for_each`: create multiple instances according to a map, or set of strings
	- `provider`: select a non-default provider configuration
	- `lifecycle`
	- `provisioner`
- When applying a Terraform configuration will:
	- Create resources that exist in the configuration but are not associated with a real infrastructure object in state.
	- Destroy resources that exist in the state but no longer exist in the configuration.
	- Update in-place resources whose arguments have changes.
	- Destroy and re-create resources whose arguments have changed but which can not be updated in-placed due to API limitations.
# Data sources
- Data sources allow Terraform to use information defined outside of Terraform defined by another separate Terraform configuration, or modified by functions.
- Data source are used to fetch data from the provider end, so that it can be used configuration in `/tf` files instead of hardcoding it.
```terraform
data "aws_ami" "std_ami" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "root-device-type"
    values = ["ebs"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}

resource "aws_instance" "myec2" {
  ami           = data.aws_ami.std_ami.id
  instance_type = "t2.micro"
}
```
## Lifecycle
- If the arguments of a data instance contain does not reference to any place, then the data instance will be read, and its state updated during Terraform's "refresh" phase, which by default runs prior to creating a plan. This ensures that the retrieved data is available for use during planning and the diff will show the real values obtained.
- Data instance arguments may refer to computed values, in which case the attributes of the instance itself cannot be resolved until all of its arguments are defined. In this case, refreshing the data instance will be deferred until the "apply" phase, and all interpolations of the data instance attributes will show as "computed" in the plan since the values are not yet known.
# Variables and Outputs
- **Input variables** are like function arguments
- **Output variables** are like function return value
- **Local values** are like function's temporary local variables
# Modules
- Modules are containers for multiple resources that are used together.
- They are the main way to package and reuse resource configurations with Terraform.
## Root module
- Every Terraform configuration has at least one module, known as its root module, which consists of the resources defined in the `.tf` files in the main working directory.
## Child modules
- A Terraform module (usually the root module of a configuration) can _call_ other modules to include their resources into the configuration. A module that has been called by another module is often referred to as a _child module._
- Child modules can be called multiple times within the same configuration, and multiple configurations can use the same child module.
# Backend block
- The `backend` defines where Terraform stores it state data files in a remote object.
## Kind
- Local: stores state on the local filesystem, locks that state using system API and performs operations locally.
- Remote: stores state snapshots and execute operation.
# Import
- Used to import existing infrastructure resources into Terraform, bringing them under Terraform's management.
# Notes
- **Resource:** Provisioning of resources/infra on our platform. Create, Update and delete!
- **Variable:** Provides predefined values as variables on our IAC. Used by resource for provisioning.
- **Data Source:** Fetch values from our infra/provider and provides data for our resource to provision infra/resource.