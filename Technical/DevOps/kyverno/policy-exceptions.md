# Policy Exceptions
## Definition
- There are often times where an organization or team must allow certain exceptions which would violate otherwise valid rules but on a one-time basis if the risks are known and acceptable.
```ad-note
PolicyExceptions are disabled by default. To enable them, set the `enablePolicyException` flag to `true`. When enabling PolicyExceptions, you must also specify which namespaces they can be used in by setting the `exceptionNamespace` flag.
```
## Structure
```yaml
apiVersion: kyverno.io/v2beta1
kind: ClusterPolicy
metadata:
  name: disallow-host-namespaces
spec:
  background: false
  rules:
    - name: host-namespaces
      match:
        any:
          - resources:
              kinds:
                - Pod
      validate:
        failureAction: Enforce
        message: >-
          Sharing the host namespaces is disallowed. The fields spec.hostNetwork,
          spec.hostIPC, and spec.hostPID must be unset or set to `false`.
        pattern:
          spec:
            =(hostPID): 'false'
            =(hostIPC): 'false'
            =(hostNetwork): 'false'
---
apiVersion: kyverno.io/v2
kind: PolicyException
metadata:
  name: delta-exception
  namespace: delta
spec:
  # which policy and rule to by pass?	
  exceptions:
    - policyName: disallow-host-namespaces
      ruleNames:
        - host-namespaces
        - autogen-host-namespaces
  # which resource get the exception?
  match:
    any:
      - resources:
          kinds:
            - Pod
            - Deployment
          namespaces:
            - delta
          names:
            - important-tool*
  # (optional) Under what extra conditions?
  conditions:
    any:
      - key: "{{ request.object.metadata.labels.app || '' }}"
        operator: Equals
        value: busybox
```
## Pod security exemptions
- There are cases where certain Pods need to be exempted from Pod Security Standards policies.
- For example, a Pod may need to run as `root` or require privileged access. In such cases, a PolicyException can be used to define an exemption for the Pod through the `podSecurity{}` block.
- It can be used to define controls that are exempted from the policy.
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
---
apiVersion: kyverno.io/v2
kind: PolicyException
metadata:
  name: pod-security-exception
  namespace: policy-exception-ns
spec:
  exceptions:
    - policyName: psa
      ruleNames:
        - restricted
  match:
    any:
      - resources:
          namespaces:
            - delta
  # allow this policy but block others
  podSecurity:
    - controlName: 'Running as Non-root'
```