# Mutate Rules
## Definition
- A `mutate` rule can be used to modify matching resources and is written as a strategic merge patch.
## JSONPatch
- Used to modify, remove or add to a request.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: policy-patch-cm
spec:
  rules:
    - name: pCM1
      match:
        any:
          - resources:
              names:
                - config-game
              kinds:
                - ConfigMap
      mutate:
        patchesJson6902: |-
          - path: "/data/ship.properties"
            op: add
            value: |
              type=starship
              owner=utany.corp
          - path: "/data/newKey1"
            op: add
            value: newValue1
```
### Structure
- `op`: operation includes `add, replace, remove`
- `path`: a JSON Pointer path to the target field
- `value`: the content to be added or used for replacement, not used for remove
### Working with lists
- Point to a position in the list, the lists are 0 indexed.
	- Example: `path: "/spec/containers/1`.
- Use `-` to apply all the action in the list without knowing the size of the list.
	- Example: `path: "/spec/tolerations/-`.
- When the value in the replacement contains a special character, use:
	- `~0` to replace `~`
	- `~1` to replace `/`
	- Example: `spec/template/metadata/annotations/config.linkerd.io~1skip-outbound-ports`
### Strategic Merge Patch
- Strategic Merge Patch is a tool that allows you to update specific items in a list (like containers, volumes, or environment variables) by using a unique identifier (like the `name`) instead of needing to know exactly where that item is located in the list. It prevents you from accidentally overwriting the entire list or breaking the structure when you only intended to change one specific container.
- When the resource is created, it will be merged with the defined value in the policy.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: strategic-merge-patch
spec:
  rules:
    - name: set-image-pull-policy-add-command
      match:
        any:
          - resources:
              kinds:
                - Pod
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              name: '{{request.object.metadata.name}}'
          spec:
            containers:
              - name: nginx
                image: nginx:latest
                imagePullPolicy: Never
                command:
                  - ls
```
#### Conditional logic using anchors
- Like with `validate` rules, conditional anchors are supported on `mutate` rules.

| Anchor             | Tag | Behavior                                      | Simplify                            |
| ------------------ | --- | --------------------------------------------- | ----------------------------------- |
| Conditional        | ()  | If then                                       | If this field has this value        |
| Add if not present | +() | If the field not exist then merge             | Add this field only if it's missing |
| Global             | <() | If the condition is true then apply the merge | If this condition is met anywhere   |
- Order of process:
	1. Check all strict conditional anchors. If any of them fail, the entire mutation stops.
	2. Then applies all Add if not present +() anchors and any plain values without anchors.
- Use `spec.emitWarning: true` to get the result message.
## Mutate Existing resources
- To mutate existing resources, the task is process by the background controller.
- The background controller needs explicit RBAC permissions to modify existing resources.
- Set the label `rbac.kyverno.io/aggregate-to-background-controller: "true"` to the role. This tells Kyverno to aggregate or add permissions to its background controller.
![](/image/Pasted%20image%2020260605163844.png)
### Mutate existing on policy creation
- By default, Kyverno policies only act on **new** requests (like `CREATE` or `UPDATE` events). If you have a policy that adds a label, and you apply it to the cluster, it will label all _future_ pods, but it will **not** retroactively go back and label all the pods already running in your cluster.
- Setting `mutateExistingOnPolicyUpdate: true` changes that behavior.
- Whenever this policy is created or updated, **scan the entire cluster for all existing resources that match this rule, and apply the mutation to them immediately.**
### Variables Referencing Target Resources
- To reference data in target resources, you can define the variable `target` followed by the path to the desired attribute. For example, using `target.metadata.labels.env` references the label `env` in the target resource.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: sync-cms
spec:
  rules:
    - name: concat-cm
      match:
        any:
          - resources:
              kinds:
                - ConfigMap
              names:
                - cmone
              namespaces:
                - foo
      mutate:
        mutateExistingOnPolicyUpdate: false
        targets:
          - apiVersion: v1
            kind: ConfigMap
            name: cmtwo
            namespace: bar
          - apiVersion: v1
            kind: ConfigMap
            name: cmthree
            namespace: bar
        patchesJson6902: |-
          - op: add
            path: "/metadata/labels/env"
            value: "{{ target.data.key }}"
```
## ForEach
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: prepend-registry
spec:
  background: false
  rules:
    - name: prepend-registry-containers
      match:
        any:
          - resources:
              kinds:
                - Pod
              operations:
                - CREATE
                - UPDATE
      mutate:
        foreach:
          - list: 'request.object.spec.containers'
            patchStrategicMerge:
              spec:
                containers:
                  - name: '{{ element.name }}'
                    image: registry.io/{{ images.containers."{{element.name}}".name}}:{{images.containers."{{element.name}}".tag}}
```