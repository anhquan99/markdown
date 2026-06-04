# Kyverno
## What is Kyverno?
- Kyverno is a k8s native policy engine.
## Why?
- Keep cluster secure and enforce security, compliance, and best practices
- Simplify policy management using k8s native resources
- Define policies using YAML
- Integrate seamlessly with GitOps and existing tools
## Architecture
- It is standing in the [admission-control](admission-control.md) component, which can mutate or validate request.
![](/image/Pasted%20image%2020260604080912.png)
## Component
![](/image/Pasted%20image%2020260604084108.png)
### Admission controller
- Acts like a security guard at the front door of the cluster.
- Receives AdmissionReview requests from the k8s API server.
- Processes the validate mutate, and `verifyImages` rules.
- Manages webhook certificates and configurations dynamically.
- Validates policy and exception resources.
- Creates work for other controllers.
### Reports controller
- Acts as cluster's compliance accountant.
- Creates PolicyReports for visibility.
- Scans existing resources for policy violations.
- Gives you a full picture of cluster compliance, not just for new resources.
### Background controller
- A worker that picks up the work tickets created by the Admission Controller.
- Design for tasks that don't need to happen immediately because it might slow the request down. Background controller takes this tasks and handles them in the background.
- Generate rule creates new resources based on a trigger. Ie: add a default NetworkPolicy to a new namespace.
- Mutate rule changes resources that are already in the cluster. Ie: add a new label to all existing deployments in the cluster.
### Cleanup controller
- Automated controller that keeps the cluster tidy by creating a special CleanupPolicy.
- Responsible for processing cleanup policies.
- Automatically deletes old or temporary resources based on a schedule.
- Creates and manages standard k8s CronJobs to delete resources.