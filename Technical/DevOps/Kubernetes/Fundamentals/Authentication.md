- K8s does not have an object called user, nor does it store username or other details in its object store. However, even without that, k8s can use username for the authentication phase of the API access control, and to request logging as well.
# Users
- **Normal users**: managed outside the k8s cluster via independent services like User/Client certificates, a file listing usernames/password, Google accounts, ...
- **Service accounts**: allows in-cluster processes to communicate with the API server to perform various operations. Most of the Service Accounts are created automatically via the API server, but they can also be created manually. The Service Accounts are tied to a particular Namespace and mount the respective credentials to communicate with the API server as Secrets.
# Authentication modules
## X509 Client Certificates
- Reference a file containing 1 or more certificate authorities by passing the `--client-ca-file=SOMEFILE` option to the API server.
- The certificate authorities mentioned in the file would validate the client certificates presented by users to the API server.
## Static Token File
- Pass a file contains pre-defined bearer tokens with `--token-auth-file=SOMEFILE` option to the API server.
- These tokens would last indefinitely, and they cannot be changed without restarting the API server.
## Bootstrap Tokens
- Tokens used for bootstrapping a new k8s cluster.
## Service Account Tokens
- Automatically enabled authenticators that use signed bearer tokens to verify requests. These tokens get attached to Pods using the Server Account Admission Controller, which allows in-cluster processes to talk to the API server,
## OpenID Connect Tokens
- Helps connect with OAuth2 providers to offload the authentication to external services.
## Webhook Token Authentication
- Bearer tokens can be offloaded to a remote service.
## Authenticating Proxy
- Allows for the programming of additional authentication logic.