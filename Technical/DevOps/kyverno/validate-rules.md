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
- Anchors allow conditional processing (i.e. “if-then-else”) and other logical checks in validation patterns.

| Anchor      | Tag | Usage                                          | Behavior                                                                                                                                                                                                                                                                                                                    |
| ----------- | --- | ---------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Conditional | ()  | Checks if the field is set to a specific value | If tag with the given value (including child elements) is specified, then peer elements will be processed.  <br>e.g. If image has tag latest then imagePullPolicy cannot be IfNotPresent.  <br>    (image): ”*:latest”  <br>    imagePullPolicy: ”!IfNotPresent”                                                            |
| Equality    | =() | Checks if the field exists                     | If tag is specified, then processing continues. For tags with scalar values, the value must match. For tags with child elements, the child element is further evaluated as a validation pattern.  <br>e.g. If hostPath is defined then the path cannot be /var/lib  <br>    =(hostPath):  <br>        path: ”!/var/lib”     |
| Existence   | ^() | Check for at least one in a list               | Works on the list/array type only. If at least one element in the list satisfies the pattern. In contrast, a conditional anchor would validate that all elements in the list match the pattern.  <br>e.g. At least one container with image nginx:latest must exist.  <br>    ^(containers):  <br>    - image: nginx:latest |
| Negation    | X() | Forbids a field                                | The tag cannot be specified. The value of the tag is not evaluated (use exclamation point to negate a value). The value should ideally be set to `"null"` (quotes surrounding null).  <br>e.g. Hostpath tag cannot be defined.  <br>    X(hostPath): “null”                                                                 |
| Global      | <() | Acts as a gatekeeper for the rule              | The content of this condition, if false, will cause the entire rule to be skipped. Valid for both validate and strategic merge patches.                                                                                                                                                                                     |
#### Conditional
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: conditional-anchor-dockersock
spec:
  background: false
  rules:
    - name: conditional-anchor-dockersock
      match:
        any:
          - resources:
              kinds:
                - Pod
      validate:
        failureAction: Enforce
        message: 'If a hostPath volume exists and is set to `/var/run/docker.sock`, the label `allow-docker` must equal `true`.'
        pattern:
          metadata:
            labels:
              allow-docker: 'true'
          (spec):
            (volumes):
              - (hostPath):
                  path: '/var/run/docker.sock'
---
# if image matches *:latest then the peer field imagePullPolicy must not be IfNotPresent
spec:
  containers:
  - (image): "*:latest"
    imagePullPolicy: "!IfNotPresent"
---
spec:
  failureAction: Enforce
  message: "If a hostPath volume exists and is set to `/var/run/docker.sock`, the label `allow-docker` must equal `true`."
  pattern:
	# else block
    metadata:
      labels:
        allow-docker: true
    # If block
    (spec):
      (volumes):
      - (hostPath):
        path: "/var/run/docker/sock"
```
#### Equality
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: equality-anchor-no-dockersock
spec:
  background: false
  rules:
    - name: equality-anchor-no-dockersock
      match:
        any:
          - resources:
              kinds:
                - Pod
      validate:
        failureAction: Enforce
        message: 'If a hostPath volume exists, it must not be set to `/var/run/docker.sock`.'
        pattern:
          =(spec):
            =(volumes):
              - =(hostPath):
                  path: '!/var/run/docker.sock'
```
#### Existence
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: existence-anchor-at-least-one-nginx
spec:
  rules:
    - name: existence-anchor-at-least-one-nginx
      match:
        any:
          - resources:
              kinds:
                - Pod
      validate:
        failureAction: Enforce
        message: 'At least one container must use the image `nginx:latest`.'
        pattern:
          spec:
            ^(containers):
              - image: nginx:latest
```
#### Global
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: sample
spec:
  rules:
    - name: check-container-image
      match:
        any:
          - resources:
              kinds:
                - Pod
      validate:
        failureAction: Enforce
        message: Images coming from corp.reg.com must use the correct imagePullSecret.
        pattern:
          spec:
            containers:
              - name: '*'
                <(image): 'corp.reg.com/*'
            imagePullSecrets:
              - name: my-registry-secret
```
### `anyPattern`
- In some cases, content can be defined at different levels. For example, a security context can be defined at the Pod or Container level. The validation rule should pass if either one of the conditions is met.
- The `anyPattern` tag is a logical OR across multiple validation patterns and can be used to check if any one of the patterns in the list match.
```ad-note
Either one of `pattern` or `anyPattern` is allowed in a rule; they both can’t be declared in the same rule.
```

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-run-as-non-root
spec:
  background: true
  rules:
    - name: check-containers
      match:
        any:
          - resources:
              kinds:
                - Pod
      validate:
        failureAction: Enforce
        message: >-
          Running as root is not allowed. The fields spec.securityContext.runAsNonRoot,
          spec.containers[*].securityContext.runAsNonRoot, and
          spec.initContainers[*].securityContext.runAsNonRoot must be `true`.
        anyPattern:
          # spec.securityContext.runAsNonRoot must be set to true. If containers and/or initContainers exist which declare a securityContext field, those must have runAsNonRoot also set to true.
          - spec:
              securityContext:
                runAsNonRoot: true
              containers:
                - =(securityContext):
                    =(runAsNonRoot): true
              =(initContainers):
                - =(securityContext):
                    =(runAsNonRoot): true
          # All containers and initContainers must define (not optional) runAsNonRoot=true.
          - spec:
              containers:
                - securityContext:
                    runAsNonRoot: true
              =(initContainers):
                - securityContext:
                    runAsNonRoot: true
```
## Deny rules
- A validate rule can deny a request based on a set of conditions written as expressions.
```yaml
validate:
  message: Main message is here.
  deny:
    conditions:
      any:
        - key: '{{ request.object.data.team }}'
          operator: Equals
          value: eng
          message: The expression team = eng failed.
        - key: '{{ request.object.data.unit }}'
          operator: Equals
          value: green
          message: The expression unit = green failed.
---
# default deny any update or create to the resource
validate:
  failureAction: Enforce
  message: "Modifying or deleting this custom resource is forbidden."
  deny: {}

```
## ForEach
- The `foreach` declaration simplifies validation of sub-elements in resource declarations, for example containers in a Pod.
### Structure
- The list field: a JMESPath expression to select the array you want to loop over.
	- Note: the path is not enclosed in `{{...}}`
- The special element variable: hold the current item from the list.
- The validation logic: applied to each element
	- `pattern`
	- `anyPattern`
	- `deny`
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: check-images
spec:
  background: false
  rules:
    - name: check-registry
      match:
        any:
          - resources:
              kinds:
                - Pod
      preconditions:
        any:
          - key: '{{request.operation}}'
            operator: NotEquals
            value: DELETE
      validate:
        failureAction: Enforce
        message: 'unknown registry'
        foreach:
          - list: 'request.object.spec.initContainers'
            pattern:
              image: 'trusted-registry.io/*'
          - list: 'request.object.spec.containers'
            pattern:
              image: 'trusted-registry.io/*'
```
## Pod security
- In Kyverno, `podSecurity` is a specialized validation rule designed to enforce the **Kubernetes Pod Security Standards (PSS)** instantly, without requiring you to write long, complex `pattern` or `deny` blocks.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: psa
spec:
  background: true
  rules:
    - name: restricted
      match:
        any:
          - resources:
              kinds:
                - Pod
      validate:
        failureAction: Enforce
        podSecurity:
          level: restricted
          version: latest
```
### Exemptions
- Exemptions within Kyverno's `podSecurity` rule are handled using the `exclude` block. This is the exact feature that makes Kyverno's implementation superior to Kubernetes' native Pod Security Admission, as it allows for surgical, highly granular exceptions rather than forcing an "all-or-nothing" approach.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: psa
spec:
  background: true
  rules:
    - name: restricted
      match:
        any:
          - resources:
              kinds:
                - Pod
      validate:
        failureAction: Enforce
        podSecurity:
          level: restricted
          version: latest
          exclude:
            - controlName: Capabilities
              images:
                - nginx*
                - redis*
```
## Common Expression Language (CEL)
- A fast, simple, and highly secure open-source programming language originally developed by Google.
- It is specifically designed to do one thing very well: **evaluate short, logical conditions** (essentially, the logic you would put inside an `if` statement).
### Features
#### It is "Non-Turing Complete" (Guaranteed to Finish)
- In standard languages, a user could accidentally (or maliciously) write an infinite `while` loop that crashes the server. CEL **does not allow loops or infinite recursion**. 
- When a program runs a CEL expression, it is mathematically guaranteed to finish evaluating in a fraction of a millisecond. This makes it incredibly safe to run user-provided rules inside critical infrastructure like an API gateway.
#### Lightning Fast
- Because it is so restricted, CEL expressions are parsed and evaluated almost instantly. When Envoy processes 10,000 requests per second, it can use CEL to check the headers of every single request without slowing down traffic.
#### Highly Embeddable
- CEL isn't a standalone program. It is designed to be embedded directly into other applications written in Go, C++, or Java. The host application (like Kubernetes) provides the data, and CEL just evaluates it.
### Available CEL variables
- `object`: the incoming resource from the admission request (null for delete)
- `oldObject`: the existing resource in the cluster (null for create)
- `request`: attribute of the admission request itself
- `namespaceObject`
- `params`: a parameter resource used for configurable policies
- `authorizer`: a cel variable to perform authorization checks
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: check-deployment-replicas
spec:
  background: false
  rules:
    - name: check-deployment-replicas
      match:
        any:
          - resources:
              kinds:
                - Deployment
      validate:
        failureAction: Enforce
        cel:
          expressions:
            - expression: 'object.spec.replicas < 4'
              message: 'Deployment spec.replicas must be less than 4.'
```
### Parameter Resources
- Parameter resources enable a policy configuration to be separated from its definition. A policy can define `cel.paramKind`, which outlines the GVK of the parameter resource, and then associate the policy with a specific parameter resource via `cel.paramRef`.
```yaml
apiVersion: rules.example.com/v1
kind: ReplicaLimit
metadata:
  name: 'replica-limit'
maxReplicas: 4
---
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: check-deployment-replicas
spec:
  background: false
  rules:
    - name: check-deployment-replicas
      match:
        any:
          - resources:
              kinds:
                - Deployment
      validate:
        failureAction: Enforce
        cel:
          paramKind:
            apiVersion: rules.example.com/v1
            kind: ReplicaLimit
          paramRef:
            name: 'replica-limit'
            parameterNotFoundAction: 'Deny'
          expressions:
            - expression: 'object.spec.replicas < params.maxReplicas'
              messageExpression: "'Deployment spec.replicas must be less than ' + string(params.maxReplicas)"
```
### CEL Preconditions
- CEL Preconditions allow for more fine-grained selection of resources than the options allowed by match and exclude statements.
```yaml
rules:
  - name: validate-nodeport-trafficpolicy
    match:
      any:
        - resources:
            kinds:
              - Service
    celPreconditions:
      - name: check-service-type
        expression: "object.spec.type.matches('NodePort')"
    validate:
      cel:
        expressions:
          - expression: "object.spec.externalTrafficPolicy.matches('Local')"
            message: 'All NodePort Services must use an externalTrafficPolicy of Local.'
```
### CEL Variables
- If an expression grows too complicated, or part of the expression is reusable and computationally expensive to evaluate. We can extract some parts of the expressions into variables. A variable is a named expression that can be referred later as variables in other expressions.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: image-matches-namespace-environment.policy.example.com
spec:
  background: false
  rules:
    - name: image-matches-namespace-environment
      match:
        any:
          - resources:
              kinds:
                - Deployment
      validate:
        failureAction: Enforce
        cel:
          variables:
            - name: environment
              expression: "'environment' in namespaceObject.metadata.labels ? namespaceObject.metadata.labels['environment'] : 'prod'"
            - name: exempt
              expression: "has(object.metadata.labels) && 'exempt' in object.metadata.labels && object.metadata.labels['exempt'] == 'true'"
            - name: containers
              expression: 'object.spec.template.spec.containers'
            - name: containersToCheck
              expression: "variables.containers.filter(c, c.image.contains('example.com/'))"
          expressions:
            - expression: "variables.exempt || variables.containersToCheck.all(c, c.image.startsWith(variables.environment + '.'))"
              messageExpression: "'only ' + variables.environment + ' images are allowed in namespace ' + namespaceObject.metadata.name"
```