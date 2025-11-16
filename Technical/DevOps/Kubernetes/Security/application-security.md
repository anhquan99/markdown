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
- Static analysis in CI/CD
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
## Container hardening
### Hardening images
- Use specific package versions.
- Don't run as root.
```Dockerfile
FROM ubuntu:24.04
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y golang-go
COPY app.go .
RUN CGO_ENABLED=0 go build app.go

FROM alpine:3.22.1
# this is where the container create non root user 
RUN addgroup -S appgroup && adduser -S appuser -G appgroup -h /home/appuser
COPY --from=0 /app /home/appuser/
# this is where the container run as non user
USER appuser
CMD ["/home/appuser/app"]
```
- Make filesystem readonly:
	- Use `chmod`
	- ...
- Remove shell access.
#### Best practices
- https://docs.docker.com/build/building/best-practices/
## Static analysis in CI/CD
- Looks at source code and text files.
- Check against rules.
- Enforce rules.
![[image-29.png]]
### Tools
- `Kubesec`
- `kube-score`
- `kube-linter`
- `conftest`
## Vulnerability scanning
### Know image vulnerabilities
- Databases:
	- https://www.cve.org/
	- https://nvd.nist.gov/
- Vulnerabilities ca be discovered in image and dependencies:
	- During build
	- At runtime
### Tools
- Clair
- Trivy