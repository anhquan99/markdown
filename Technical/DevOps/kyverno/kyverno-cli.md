# Kyverno CLI
## Definition
- A standalone, command-line tool for interacting with Kyverno policies and resources outside a cluster.
## Why?
- Test policy locally
- Instant feedback
- CI/CD integration
- Shift left
## Command
### Apply
```bash
kyverno apply {path-to-policy.yaml} --resource {path-to-resource.yaml}
```
#### Testing against a live cluster
- Use the `--cluster` or `-c`.
- Kyverno cli will use the current kubeconfig context to connect to a live cluster.
- Do not use the `--resouce` with the cluster flag.
#### Testing policy exception
- Use the `--exception` or `-e`.
#### Value file
- File containing values for policy variables.
- Use `--values-file` of `-f`.
### Test
- Usage like unit test has a test suit.
```yaml
apiVersion: cli.kyverno.io/v1alpha1
kind: Test
metadata:
  name: disallow-latest-tag
policies:
  - policy.yaml
resources:
  - pod-fail.yaml
  - pod-pass.yaml
results:
  - policy: disallow-latest-tag
    rule: require-image-tag
    resource: bad-pod
    kind: Pod
    result: fail
  - policy: disallow-latest-tag
    rule: require-image-tag
    resource: good-pod
    kind: Pod
    result: pass
```
- For the mutate rule the result has addition field `patchedResource` that point to the expected file.
- For the mutate rule the result has addition field `generatedResource` that point to the expected file.
- Run the test suit: `kyverno test`.