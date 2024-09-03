*HashiCorp Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share.*
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
