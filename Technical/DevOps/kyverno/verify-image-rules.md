# Verify Image Rules
# Definition
- The `verifyImages` rule is Kyverno's specialized mechanism for **Software Supply Chain Security**. Instead of checking what a Pod _is_ (like its labels or security context), this rule checks _where_ the Pod came from and _if_ it is authentic.
- It ensures that the container images running in your cluster have been cryptographically signed by a trusted authority. This prevents attackers from injecting malicious code into your supply chain (a "Man-in-the-Middle" attack on your images).
## How Kyverno verifies image
1. Intercepts the request; as the verifier, it uses its integrations (e.g with Cosign)
2. Connects to the Container Registry to fetch the signature
3. Uses a Public key to check if the signature is valid
4. Allows or denies the pod creation
## Structure
![](/image/Pasted%20image%2020260606052952.png)
- `type`: the signature type. Sigstore Cosign and Notary are supported.
- `imageReferences`: a list of image reference patterns to match
- `skipImageReferences`: a list of image reference patterns that should be skipped.
- `required`: enforces that all matching images are verified
- `mutateDigest`: converts tags to digests for matching images
- `verifyDigest`: enforces that digests are used for matching images
- `repository`: use a different repository for fetching signatures
- `imageRegistryCredentials`: use specific registry credentials for this policy.
- `attestors`: represent trusted source.
	- `count` is not specified (default): all entries must pass. AND condition
	- `count: 1`: at least one of the entries must pass. OR condition.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: verify-images-production
spec:
  rules:
    - name: verify-image
      match:
        any:
          - resources:
              kinds:
                - Pod
      verifyImages:
        - imageReferences:
            - 'registry.example.com/production/*'
          imageRegistryCredentials:
            secrets:
              - 'production/registry-pull-secret'
          attestors:
            - entries:
                - keys:
                    publicKeys: |
                      -----BEGIN PUBLIC KEY-----
                      ...
                      -----END PUBLIC KEY-----
```
## Sigstore
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: check-image
spec:
  webhookConfiguration:
    failurePolicy: Fail
    timeoutSeconds: 30
  background: false
  rules:
    - name: check-image
      match:
        any:
          - resources:
              kinds:
                - Pod
      verifyImages:
        - imageReferences:
            - 'ghcr.io/kyverno/test-verify-image*'
          failureAction: Enforce
          attestors:
            - count: 1
              entries:
                - keys:
                    publicKeys: |-
                      ..
                    rekor:
                      ignoreTlog: true
                      url: https://rekor.sigstore.dev
```
## Notary
```yaml
apiVersion: kyverno.io/v2beta1
kind: ClusterPolicy
metadata:
  name: check-image-notary
spec:
  webhookConfiguration:
    failurePolicy: Fail
    timeoutSeconds: 30
  rules:
    - name: verify-signature-notary
      match:
        any:
          - resources:
              kinds:
                - Pod
      verifyImages:
        - type: Notary
          imageReferences:
            - 'ghcr.io/kyverno/test-verify-image*'
          failureAction: Enforce
          attestors:
            - count: 1
              entries:
                - certificates:
                    cert: |-
                     ...
```
## Caching
- When an image is successfully verified, the positive result is cached by default.
- Subsequent requests for the same image will use the cached result, skipping the network calls.
- This speeds up policy evaluation for frequently used image.
- Cache flags:
	- `imageVerifyCacheEnabled` default true
	- `imageVerifyCacheMaxSize` default 1000
	- `imageVerifyCacheTTLDuration` default 60