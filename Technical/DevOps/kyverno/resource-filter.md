# Resource Filter
## Resource types
- `resources`: select resources by names, namespaces, kinds, operations, label selectors, annotations, and namespace selectors.
- `subjects`: select users, user groups, and service accounts
- `roles`: select namespaced roles
- `clusterRoles`: select cluster wide roles
```yaml
match:
  any:
    - resources:
        names:
          - 'prod-*'
          - 'staging'
        kinds:
          - Service
        operations:
          - CREATE
    - resources:
        kinds:
          - Service
        operations:
          - CREATE
      subjects:
        - kind: User
          name: dave
```
- In addition, a user may specify the `group` and `apiVersion` with a kind in the `match` / `exclude` declarations for a policy rule, known as GVK format. Supported formats:
	- `Group/Version/Kind`
	- `Version/Kind`
	- `Kind`
- Conditions inside `clusterRoles`, `roles`, and `subjects` are always evaluated using a logical OR operation, as each request can only have a single instance of these values.
## Match statements
- Define primary targets for policy rule to tell policy what to look for.
- Kinds field is mandatory.
```yaml
match:
  any:
    - resources: # resource must is service AND have a name AND operation is create
        kinds:
          - Service
        names:
          - staging # staging OR dev
		  - dev
        operations:
          - CREATE
    - resources:
        kinds:
          - Service
        namespaces:
          - prod
        operations:
          - CREATE
```
- The operation inside the list is `OR` and between filter is `AND` when the expression is `any`.
### Expression
- `any`: means all the filters work on operation `OR` on multiple resources.
- `all`: means all the filters work on operation `AND` on multiple resources.
### Matching types
- Label: `matchLabels`
```yaml
match:
  any:
    - resources:
      kinds:
        - Pod
      selector:
        matchLabels:
          app: critical
```
- Namespace: `namespaceSelector`
	-  `matchLabels`
	- `matchExpressions`
```yaml
match:
  any:
    - resources:
        kinds:
          - Pod
        namespaceSelector:
          matchLabels:
            organization: engineering
---
match:
  any:
    - resources:
        kinds:
          - Pod
        namespaceSelector:
          matchExpressions:
            - key: namespacekind
              operator: NotIn
              values:
                - platform
                - ci
```

```ad-note
When the `operations` field is absent, the default behavior is to match on CREATE and UPDATE requests.
```
## Exclude statement
- Opposite to the match statement.
- If the resource matches the match statement AND matches the exclude block then it is skipped.
- Exclude statement have `any` and `all` clause.
## Precondition statement
- Use [jmespath](jmespath.md) to get data from the request and evaluate with the policy.
- Precondition statement have `any` and `all` clause.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: any-all-preconditions
spec:
  background: false
  rules:
    - name: any-all-rule
      match:
        any:
          - resources:
              kinds:
                - Deployment
      preconditions:
        any:
          - key: "{{ request.object.metadata.labels.color || '' }}"
            operator: Equals
            value: blue
          - key: "{{ request.object.metadata.labels.app || '' }}"
            operator: Equals
            value: busybox
      validate:
        failureAction: Enforce
        message: 'Busybox must be used based on this label combination.'
        pattern:
          spec:
            template:
              spec:
                containers:
                  - name: '*busybox*'
```
### Operators
- Equality: `Equals, NotEquals`
- Set-based: `AnyIn, AllIn, AnyNotIn, AllNotIn`
- Numeric/quantity: `GreaterThan, LessThan`
- Time-based: `DurationGreaterThan, DurationLessThan`