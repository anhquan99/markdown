# Policy Report
## Definition
- Policy reports are Kubernetes Custom Resources, generated and managed automatically by Kyverno, which contain the results of applying matching Kubernetes resources to Kyverno ClusterPolicy or Policy resources.
- Policy reports are created based on two different triggers: an admission event (a `CREATE`, `UPDATE`, or `DELETE` action performed against a resource) or the result of a background scan discovering existing resources.
- Policy report can have both namespace and cluster scope.
- Reports controller is dedicated controller which is responsible for creating and updating all policy reports.
## Report logic
| Result | Description                                                                                                                                      |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| pass   | The resource was applicable to a rule and the pattern passed evaluation.                                                                         |
| skip   | Preconditions were not satisfied (if applicable) in a rule, or an applicable PolicyException exists and so further processing was not performed. |
| fail   | The resource failed the pattern evaluation.                                                                                                      |
| warn   | The annotation `policies.kyverno.io/scored` has been set to `"false"` in the policy converting otherwise `fail` results to `warn`.               |
| error  | Variable substitution failed outside of preconditions and elsewhere in the rule (ex., in the pattern).                                           |
```yaml
apiVersion: wgpolicyk8s.io/v1alpha2
kind: PolicyReport
metadata:
  creationTimestamp: '2023-12-06T13:19:03Z'
  generation: 2
  labels:
    app.kubernetes.io/managed-by: kyverno
  name: 487df031-11d8-4ab4-b089-dfc0db1e533e
  namespace: kube-system
  ownerReferences:
    - apiVersion: v1
      kind: Pod
      name: kube-apiserver-kind-control-plane
      uid: 487df031-11d8-4ab4-b089-dfc0db1e533e
  resourceVersion: '720507'
  uid: 0ec04a57-4c3d-492d-9278-951cd1929fe3
results:
  - category: Pod Security Standards (Baseline)
    message:
      'validation error: Sharing the host namespaces is disallowed. The fields
      spec.hostNetwork, spec.hostIPC, and spec.hostPID must be unset or set to `false`.
      rule host-namespaces failed at path /spec/hostNetwork/'
    policy: disallow-host-namespaces
    result: fail
    rule: host-namespaces
    scored: true
    severity: medium
    source: kyverno
    timestamp:
      nanos: 0
      seconds: 1701868762
# ...
scope:
  apiVersion: v1
  kind: Pod
  name: kube-apiserver-kind-control-plane
  namespace: kube-system
  uid: 487df031-11d8-4ab4-b089-dfc0db1e533e
summary:
  error: 0
  fail: 1
  pass: 10
  skip: 0
  warn: 0
```
## Background scans
- The process of periodically applying policies to existing resources to generate reports.
- Trigger by the `background` field in the Policy. It is set to true by default.
- `backgroundScanInterval` is 1h.
```ad-note
Background scans are handled by the reports controller and not the background controller.
```

- Behavior when `background: true`

|                          | New Resource | Existing Resource |
| ------------------------ | ------------ | ----------------- |
| `failureAction: Enforce` | Pass only    | Report            |
| `failureAction: Audit`   | Report       | Report            |
- Behavior when `background: false`

|                          | New Resource | Existing Resource |
| ------------------------ | ------------ | ----------------- |
| `failureAction: Enforce` | Pass only    | None              |
| `failureAction: Audit`   | Report       | None              |
- Limits: background scanning cannot use information that is only available during an admission request.
## Notes
### Are PolicyReports generated if a resource was blocked in case the spec.validationFailureAction is set to Enforce?
- When you set `validationFailureAction: Enforce`, Kyverno operates as a **Kubernetes Admission Webhook**. This is a "gatekeeper" process that runs **before** the resource is ever saved to the Kubernetes API database (etcd).
	1. **Request Arrives:** A user tries to `kubectl apply` a Pod.
	2. **Kyverno Intercepts:** The API server asks Kyverno, "Is this allowed?"
	3. **Policy Evaluation:** Kyverno checks the request against your policy.
	4. **The Decision:** Since it is in `Enforce` mode and the policy fails, Kyverno tells the API server: **"Reject this request."**
	5. **Termination:** The API server denies the request and returns an error to the user. **The resource is never created.**