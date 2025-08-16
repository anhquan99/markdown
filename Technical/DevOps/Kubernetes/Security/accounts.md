# Account
## Service account
- Service account is a type of non-human account, used for machines and managed by k8s API.   
- Namespaced.
- There is a default SA in every namespace used by pods.
- Can be used to talk to k8s API.
## Users and certificates
- There is no k8s User resource, a user is someone holds a certificate and key.
- Client certificate:
	- Certificate signed by the cluster's certificate authority (CA)
	- Username under common name /CN=username
- To create a user follow the https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
### Leak and invalidation
- There is no way to invalid a certificate.
- If a certificate has been leaked:
	- Remove all access via RBAC
	- Username cannot be used until cert expired
	- Create new CA and re-issue all certs