# Container Security
## Container runtime
- The engine running the images needs to be secured.
- Options:
	- gVisor
	- Kata
	- Firecracker
	- Unik
## `RuntimeClass`
- The `RuntimeClass` is a feature which needs to be enabled on both `kube-apisever` and `kubeletes` on every node that use runtime. While you can create the object in the API server, and declare the engine to start when a pod requests it, without the backend being configured, the pod will never reach the **ready** state.
```yaml
# cluster
apiVersion: node.k8s.io/v1  
kind: RuntimeClass  
metadata:  
name: gvisor  
handler: runsc
...

# conatiner
...
spec:  
runtimeClassName: gvisor #<<--This must match the name of the runtime above  
containers:
...
```
## gVisor
- A Go written sandbox often used along with rule-based execution tools such as SELinux or `seccomp`.
- It provides an independent kernel between the host and the containerized application.
- Each container has dedicated gVisor, which consists of 2 processes:
	- Sentry: handles all of the kernel functionality the container requires.
	- Gofer: handles access to filesystem, and runs in a restricted `seccomp` container.
- These 2 processes communicate with each other using the 9P protocol. This approach offers isolation similar to virtual machines but less overhead.
- As gVisor intercepts the application system calls, there may be some application compatibility concerns and higher overhead per system call. As result, applications with system call intensive workloads may not perform well using gVisor.
## Kata
- Unlike traditional containers which share a host kernel and use namespaces to keep isolated, Kata leverage hardware virtualization, to provide per-container kernels. This provides an extra layer of isolation for workload and security concerns.
![[image-15.png]]
## Trusted packages
- Software should be drawn from trusted sources and checked to ensure they have not changed since creation.
- Options:
	- The Update Framework
	- Uptane
	- Notary
## Centralize tools
- The easiest way to implement policy-based access is to setup the infrastructure before the cluster is in use.
- Options: Open Policy Agent (OPA).
- The Gatekeeper project uses admission controller and the OPA Constraint Framework to act as a bridge between the API server and OPA.
- Policies are used to make decisions when presented with structured documents.
## Gatekeeper
![[image-16.png]]
## Constraint template
- A constraint template is used to create a Custom Resource Definition (CRD) which extends the OPA policy library. It defines the object which will then be called via the `constraint`.
```yaml
apiVersion: templates.gatekeeper.sh/v1  
kind: ConstraintTemplate  
metadata:  
  name: k8srequiredlabels  
spec:  
  crd:  
    spec:  
      names:  
        kind: K8sRequiredLabels  
      validation:  
        openAPIV3Schema:  
          properties:  
            labels:  
              type: array  
              items: string
...
targets:  
  - target: admission.k8s.gatekeeper.sh  
    rego: |  
      package k8srequiredlabels** 

      **violation[{"msg": msg, "details": {"missing_labels": missing}}] {  
        provided := {label | input.review.object.metadata.labels[label]}  
        required := {label | label := input.parameters.labels[_]}  
    missing := required - provided  
        count(missing) > 0  
        msg := sprintf("You must provide labels: %v", [missing])  
      }
# the second half of the constraint template declares the target which will be responsible for passing along the API information.
```
### Constraint
```yaml
apiVersion: constraints.gatekeeper.sh/v1  
kind: K8sRequiredLabels  
metadata:  
  name: ns-require-label  
spec:  
  match:  
    kinds:  
 - apiGroups: [""]  
        kinds: ["Namespace"]  
  parameters:  
    labels: ["gk-ns"]
```