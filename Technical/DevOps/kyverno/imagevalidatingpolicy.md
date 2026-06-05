# ImageValidatingPolicy
# Definition
- A Kyverno policy type designed for verifying container image signatures and attestations.
## How Kyverno verifies image
1. Intercepts the request; as the verifier, it uses its integrations (e.g with Cosign)
2. Connects to the Container Registry to fetch the signature
3. Uses a Public key to check if the signature is valid
4. Allows or denies the pod creation
## Additional fields
### `images`
- When `Kubernetes` resources are evaluated images for pods and pod templates are automatically extracted for processing.
```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: sample
spec:
  evaluation:
    mode: JSON
  images:
    - name: imagerefs
      expression: '[object.imageReference]'
  # ...
```
### `matchImageReferences`
- The `spec.matchImageReferences` field defines rules for matching container images. It allows specifying glob patterns or CEL expressions that specify which images the policy should match.
```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: check-images
spec:
  matchImageReferences: # At least one sub-field is required
    - glob: 'ghcr.io/kyverno/*' # Match images using glob pattern
    - expression: "image.registry == 'ghcr.io'" # Match using CEL expression
  # ...
```
### `attestors`
- The `attestors` field declares trusted signing authorities, such as keys or certificates.
- **Cosign attestors:** These use public keys, keyless signing, transparency logs, certificates, or TUF-based metadata for image validation.
- **Notary attestors:** These use certificates and optional Time Stamp Authority (TSA) certificates for image signature verification.
```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: check-images
spec:
  matchConstraints:
    resourceRules:
      - apiGroups:
          - ''
        apiVersions:
          - v1
        operations:
          - CREATE
        resources:
          - pods
  variables:
    - name: cm
      expression: 'resource.Get("v1", "configmaps", object.metadata.namespace, "keys")'
  matchImageReferences:
    - glob: ghcr.io/*
  attestors:
    - name: notary
      notary:
        certs:
          value: |
            -----BEGIN CERTIFICATE-----
            MIIBjTCCATOgAwIBAgIUdMiN3gC...
            -----END CERTIFICATE-----
          expression: variables.cm.data.cert
        tsaCerts:
          value: |
            -----BEGIN CERTIFICATE-----
            MIIC4jCCAcqgAwIBAgIQAm3T2tWk...
            -----END CERTIFICATE-----
          expression: variables.cm.data.tsaCert
```
### `validationConfigurations`
- The `validationConfigurations` field defines settings for mutating image tags to digests, verifying that images are using digests, and enforcing image validation requirements across policies.
```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: check-images
spec:
  matchImageReferences:
    - glob: ghcr.io/*
  validationConfigurations:
    mutateDigest: true # Mutates image tags to digests (recommended to avoid mutable tags).
    required: true # Enforces that images must be validated according to policies.
    verifyDigest: true # Ensures that images are verified with a digest instead of tags.
```
### `credentials`
- Credentials specify the authentication information required to securely access and interact with a registry.
```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: check-images
spec:
  matchImageReferences:
    - glob: ghcr.io/*
  credentials:
    allowInsecureRegistry: false # Deny insecure access to registries
    providers: # specifies whose authentication providers are provided
      - 'default'
      - 'google'
      - 'azure'
      - 'amazon'
      - 'github'

    secrets:
      - 'my-registry-secret' # Secrets specifies a list of secrets that are provided for credentials. Secrets must live in the Kyverno namespace.
```