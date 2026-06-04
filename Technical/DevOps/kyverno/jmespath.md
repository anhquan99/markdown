# JMESPath
## Definition
- A query language for JSON data.
- Allows to navigate and extract values from deep inside a JSON document.
- Kyverno get information about the request in the AdmissionReview object from API server.
## Syntax
- Expression: {{...}}
- Go deeper structure by using dot `.`.
- Example: `{{ request.object.spec.replicas }}`
## Predefined variables
- Kyverno has some predefined variables to easy access.
- Example:
	- `{{ serviceAccountName }}`
	- `{{ serviceAccountNamespace }}`
	- `{{ request.roles }}`
	- `{{ request.clusterRoles }}`
	- `{{ images }}`
- If Kyverno is checking the live request (admission request) then some of the variables are not available on the time because it only exists on the background job so the `background: false` is required.
### Non-existence check
- Use the `OR` operator to check null.
- Example: `{{ request.object.metadata.labels.app || '' }}`