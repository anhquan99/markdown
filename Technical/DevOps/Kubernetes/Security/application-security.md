# Application Security
## Practices
- Use Secrets / no hardcoded credentials
- RBAC
- Container Sandboxing
- Container hardening
	- Attack surface
	- Run as user
	- Readonly filesystem
- Vulnerability scanning
- mTLS/ ServiceMeshes
## mTLS
- Mutual authentication.
- Two-way (bilateral) authentication.
- 2 parties authenticating each other at the same time.
### ServiceMesh / Proxy
- Communication is based on mTLS. It requires certificates of both side, which are managed and rotate by the ServiceMesh. 
![[image-28.png]]
- Communication workflow example the app container in pod1 want to communicate to the app container in pod2:
	1. The app container in pod1 can't send traffic to outside, the traffic is redirected to the proxy container in pod1.
	2. The proxy container in pod1 will encrypt the traffic from app in pod1 using certificate managed by the External manager (CNI) and send traffic to pod2. The traffic in app don't need to know about the certificates, TLS, or simply run HTTP.
	3. The traffic come to pod2 which will be received and decrypt by the proxy container in pod2.
	4. Finally the traffic is sent to app container in pod2.
![[image-27.png]]