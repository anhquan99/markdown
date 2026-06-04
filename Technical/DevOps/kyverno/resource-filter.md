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
- `any`: means all the filters work on operation `OR`.
- `all`: means all the filters work on operation `AND`.
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