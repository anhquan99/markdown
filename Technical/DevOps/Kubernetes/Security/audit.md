# Audit
![[image-32.png]]
## Problem
- Did someone access an important secret while it was not protected?
- When was the last time that user X did access cluster Y?
- Does my CRD work properly?
## K8s API request stages
- `RequestReceived` - The stage for events generated as soon as the audit handler receives the request, and before it is delegated down the handler chain.
- `ResponseStarted` - Once the response headers are sent, but before the response body is sent. This stage is only generated for long-running requests (e.g. watch).
- `ResponseComplete` - The response body has been completed and no more bytes will be sent.
- `Panic` - Events generated when a panic occurred.
## Audit levels
- `None` - don't log events that match this rule.
- `Metadata` - log events with metadata (requesting user, timestamp, resource, verb, etc.) but not request or response body.
- `Request` - log events with request metadata and body but not response body. This does not apply for non-resource requests.
- `RequestResponse` - log events with request metadata, request body and response body. This does not apply for non-resource requests.
## Audit policy
- Define rules about what events should be recorded and what data they should include.
- The first matching rule sets the **audit level** of the event.
```yaml
apiVersion: audit.k8s.io/v1 # This is required.
kind: Policy
# Don't generate audit events for all requests in RequestReceived stage.
omitStages:
  - "RequestReceived"
rules:
  # Log pod changes at RequestResponse level
  - level: RequestResponse
    resources:
    - group: ""
      # Resource "pods" doesn't match requests to any subresource of pods,
      # which is consistent with the RBAC policy.
      resources: ["pods"]
  - level: Metadata
    # Long-running requests like watches that fall under this rule will not
    # generate an audit event in RequestReceived.
    omitStages:
      - "RequestReceived"
```
## Log location
![[image-33.png]]