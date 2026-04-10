# ArgoCD
## Definition
- Argo CD is a declarative GitOps-based continuous delivery tool for Kubernetes.
- Argo CD is a GitOps tool implemented as a Kubernetes controller that continuously tracks applications and compares their current state to the target state defined in a Git repository.
## How it works
- ArgoCD uses Git as the source of truth for the desired application state and automates synchronization between the desired and the target environment.
- When it detects that a deployed application’s live state deviates from its desired state, it marks the application as out of sync
- Argo CD executes the later GitOps stages and ensures the correct deployment of new configurations to Kubernetes clusters. It is a Kubernetes-native tool for implementing continuous deployment. It differs from external CD tools, which only support push-based deployment because it can pull the updated code from a Git repository and deploy it directly to a Kubernetes resource
  ![](/Image/pasted-image-20240902143901.png)
## When to use ArgoCD
- Declarative and Git based management
- Continuous operations with monitoring and remediation
- Enterprise ready
## Supported kubernetes manifest extensions
- Kustomize
- Helm charts
- Ksonnet applications
- Jsonnet files
- Yaml/Json manifest
## Features
- Automated deployment of applications to specified target environment in multiple clusters.
- SSO integration
- Health status analysis of application resources
- Automated configuration drift detection and visualization
- Audit trails
- Webhook integration
- CLI and access token for automation and CI integration
- Automated or manual syncing of applications to its desired state
## Argo projects

- Argo CD (GitOps controller)
- Argo Rollouts (Progressive Delivery controller)
- Argo Workflows (Workflow engine for Kubernetes)
- Argo Events (Event handling for Kubernetes)
## Resources
- **Application:** a Kubernetes resource that defines a deployed software instance.
- **Project:** an enforcing rules for an application. Each application is assigned with a project. 
- **Application set:** is used for generating a ArgoCD applications for multiple environments or multi tenants.
