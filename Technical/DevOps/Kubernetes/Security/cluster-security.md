# Cluster Security
## Practice
- K8s components are running secure and up-to-date
	- `Apiserver`
	- `Kubelet`
	- `ETCD`
- Restrict (external) access
- Use Authentication → Authorization
- `AdmissionControllers`
	- `NodeRestriction`
	- `CustomPolicies` (OPA)
- Enable Audit logging
- Secure benchmarking
## OPA
- Use in the [access management](access-management.md) in the [admission control](admission-control.md) stage.
- OPA is an open source, general-purpose policy engine that enables unified, context-aware policy enforcement across the entire stack.
- Not k8s specific.
- Easy implementation of policy using Rego language.
- Works with Json/yaml.
- Does not know concepts like pods or deployments.
### OPA Gatekeeper
- Provides k8s CRDs (Custom Resource Definition):
	- ConstraintTemplate
	- Contraint