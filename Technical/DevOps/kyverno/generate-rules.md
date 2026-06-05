# Generate Rules
## Definition
- A generate rule can be used to create new Kubernetes resources in response to some other event including things like resource creation, update, or delete, or even by creating or updating a policy itself.
- This is useful to create supporting resources, such as new RoleBindings or NetworkPolicies for a Namespace or perform other automation tasks that may either require other tools or be scripted.
## Data source
- The source of a generated resource may be defined in the Kyverno policy/rule directly.
- Clone is the rule copies an existing resource from the cluster.
- The resources created by a generate rule are called downstream resource.
- Prerequisite is to grant permission for Kyverno to generate resources.
	- `rbac.kyverno.io/aggregate-to-admission-controller`
	- `rbac.kyverno.io/aggregate-to-background-controller`
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: default
spec:
  rules:
    - name: deny-all-traffic
      match:
        any:
          - resources:
              kinds:
                - Namespace
      exclude:
        any:
          - resources:
              namespaces:
                - kube-system
                - default
                - kube-public
                - kyverno
      generate:
        kind: NetworkPolicy
        apiVersion: networking.k8s.io/v1
        name: deny-all-traffic
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            # select all pods in the namespace
            podSelector: {}
            policyTypes:
              - Ingress
              - Egress
```
### Synchronize resource
- Manage resource after it is create with `synchronize` attribute.
- When the attribute is set to true then Kyverno continues to link between the policy and the downstream resource.
- Behaviors:
	- Self-heals on mismatch
	- Propagates policy updates
	- Automatic cleanup

| Action             | Sync Effect                                                              | NoSync Effect         |
| ------------------ | ------------------------------------------------------------------------ | --------------------- |
| Delete Downstream  | Downstream recreated                                                     | Downstream deleted    |
| Delete Rule/Policy | Downstream retained  <br>`generate.orphanDownstreamOnPolicyDelete: true` | Downstream retained   |
| Delete Rule/Policy | Downstream deleted  <br>`generate.orphanDownstreamOnPolicyDelete: false` | Downstream retained   |
| Delete Trigger     | Downstream deleted                                                       | None                  |
| Modify Downstream  | Downstream reverted                                                      | Downstream modified   |
| Modify Rule/Policy | Downstream synced                                                        | Downstream unmodified |
| Modify Trigger     | Downstream deleted                                                       | None                  |
## Clone source
- When a generate policy should take the source from a resource which already exists in the cluster, a `clone` object is used instead of a `data` object.
- When triggered, the generate policy will clone from the resource name and location defined in the rule to create the new resource.
### Synchronize
| Action             | Sync Effect           | NoSync Effect         |
| ------------------ | --------------------- | --------------------- |
| Delete Downstream  | Downstream recreated  | Downstream deleted    |
| Delete Rule/Policy | Downstream retained   | Downstream retained   |
| Delete Source      | Downstream deleted    | Downstream retained   |
| Delete Trigger     | Downstream deleted    | None                  |
| Modify Downstream  | Downstream reverted   | Downstream modified   |
| Modify Rule/Policy | Downstream unmodified | Downstream unmodified |
| Modify Source      | Downstream synced     | Downstream unmodified |
| Modify Trigger     | Downstream deleted    | None                  |
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: sync-secrets
spec:
  rules:
    - name: sync-image-pull-secret
      match:
        any:
          - resources:
              kinds:
                - Namespace
      generate:
        apiVersion: v1
        kind: Secret
        name: regcred
        namespace: '{{request.object.metadata.name}}'
        synchronize: true
        clone:
          namespace: default
          name: regcred
```
## Clone multiple resources
- Use `cloneList`.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: sync-secret-with-multi-clone
spec:
  rules:
    - name: sync-secret
      match:
        any:
          - resources:
              kinds:
                - Namespace
      exclude:
        any:
          - resources:
              namespaces:
                - kube-system
                - default
                - kube-public
                - kyverno
      generate:
        namespace: '{{request.object.metadata.name}}'
        synchronize: true
        cloneList:
          namespace: staging
          kinds:
            - v1/Secret
            - v1/ConfigMap
          selector:
            matchLabels:
              allowedToBeCloned: 'true'
```
## Generate for existing resources
- Kyverno supports generation for existing resources. Generate existing policies are applied when the policy is created and in the background which creates target resources based on the match statement within the policy.
- Use `generate[*].generateExisting` set to true.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-resources
spec:
  rules:
    - name: generate-existing-networkpolicy
      match:
        any:
          - resources:
              kinds:
                - Namespace
      generate:
        generateExisting: true
        kind: NetworkPolicy
        apiVersion: networking.k8s.io/v1
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        synchronize: true
        data:
          metadata:
            labels:
              created-by: kyverno
          spec:
            podSelector: {}
            policyTypes:
              - Ingress
              - Egress
```