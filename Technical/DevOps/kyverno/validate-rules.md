# Validate Rules
## Basic validation
```yaml
apiVersion: kyverno.io/v1
# The `ClusterPolicy` kind applies to the entire cluster.
kind: ClusterPolicy
metadata:
  name: require-ns-purpose-label
# The `spec` defines properties of the policy.
spec:
  # The `rules` is one or more rules which must be true.
  rules:
    - name: require-ns-purpose-label
      # The `match` statement sets the scope of what will be checked. In this case, it is any `Namespace` resource.
      match:
        any:
          - resources:
              kinds:
                - Namespace
      # The `validate` statement tries to positively check what is defined. If the statement, when compared with the requested resource, is true, it is allowed. If false, it is blocked.
      validate:
        # The `failureAction` tells Kyverno if the resource being validated should be allowed but reported (`Audit`) or blocked (`Enforce`).
        failureAction: Enforce
        # The `message` is what gets displayed to a user if this rule fails validation.
        message: 'You must have label `purpose` with value `production` set on all new namespaces.'
        # The `pattern` object defines what pattern will be checked in the resource. In this case, it is looking for `metadata.labels` with `purpose=production`.
        pattern:
          metadata:
            labels:
              purpose: production
```
## Failure action
-  `failureAction` or `validationFailureAction` (deprecated) attributes control admission control behaviors for resources that are not compliant with a policy.
### Types
- `Enforce`: resource creation or updates are blocked when the resource does not comply.
	- When set `allowExistingViolations` to false the update to preexisting resources which violate that rule will be blocked.
- `Audit`: a policy violation is logged in a `PolicyReport` or `ClusterPolicyReport` but the resource creation or update is allowed.
	- To show warning to the user, use set the `emitWarning: true`
### Override
- Using `failureActionOverrides`, you can specify which actions to apply per Namespace. This attribute is only available for ClusterPolicies.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: check-label-app
spec:
  rules:
    - name: check-label-app
      match:
        any:
          - resources:
              kinds:
                - Pod
      validate:
        failureAction: Audit
        failureActionOverrides:
          - action: Enforce # Action to apply
            namespaces: # List of affected namespaces
              - default
          - action: Audit
            namespaces:
              - test
        message: 'The label `app` is required.'
        pattern:
          metadata:
            labels:
              app: '?*'
```
## Pattern
- A validation rule which checks resource data is defined as an overlay pattern that provides the desired configuration.
### Wildcards
- `*` - matches zero or more alphanumeric characters
- `?` - matches a single alphanumeric character
- `?*` - field must exist and have non-empty value
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: all-containers-need-requests-and-limits
spec:
  rules:
    - name: check-container-resources
      match:
        any:
          - resources:
              kinds:
                - Pod
      validate:
        failureAction: Enforce
        message: 'All containers must have CPU and memory resource requests and limits defined.'
        pattern:
          spec:
            containers:
              # Select all containers in the pod. The `name` field here is not specifically required but serves
              # as a visual aid for instructional purposes.
              - name: '*'
                resources:
                  limits:
                    # '?' requires 1 alphanumeric character and '*' means that
                    # there can be 0 or more characters. Using them together
                    # e.g. '?*' requires at least one character.
                    memory: '?*'
                  requests:
                    memory: '?*'
                    cpu: '?*'
```
### Operators
- Operators in the following support list values in addition to scalar values. 

| Operator | Meaning                   |
| -------- | ------------------------- |
| `>`      | greater than              |
| `<`      | less than                 |
| `>=`     | greater than or equals to |
| `<=`     | less than or equals to    |
| `!`      | not equals                |
| `\|`     | logical or                |
| `&`      | logical and               |
| `-`      | within a range            |
| `!-`     | outside a range           |
```ad-note
There is no operator for `equals` as providing a field value in the pattern requires equality to the value.
```

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: validate
spec:
  rules:
    - name: validate-replica-count
      match:
        any:
          - resources:
              kinds:
                - Deployment
      validate:
        failureAction: Enforce
        message: 'Replica count for a Deployment must be greater than or equal to 2.'
        pattern:
          spec:
            replicas: '>=2'
```
### Anchors
- 