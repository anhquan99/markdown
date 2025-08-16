# Account
## Service account
- 
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