# Crossplane
```yaml
input:
  apiVersion: pt.fn.crossplane.io/v1beta1
  kind: Resources
  resources:
    - name: resource-name
      base: { ... }
      patches:
        - type: FromCompositeFieldPath    # XR → Resource
        - type: ToCompositeFieldPath      # Resource → XR
        - type: CombineFromComposite      # Multiple XR → Resource
```

**Transform Types:**

| Transform       | Purpose           | Example              |
| --------------- | ----------------- | -------------------- |
| `map`           | Value mapping     | `free` → `us-east-1` |
| `string.Format` | String formatting | `%s-config`          |
| `convert`       | Type conversion   | `int` → `string`     |
| `math.Multiply` | Arithmetic        | `GB` → `bytes`       |

**Patch Types:**

| Patch                    | Direction              | Use Case                         |
| ------------------------ | ---------------------- | -------------------------------- |
| `FromCompositeFieldPath` | XR → Resource          | Set resource fields from XR spec |
| `ToCompositeFieldPath`   | Resource → XR          | Write status/outputs to XR       |
| `CombineFromComposite`   | Multiple XR → Resource | Combine fields (e.g., name-env)  |