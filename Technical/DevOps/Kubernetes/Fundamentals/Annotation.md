# Annotation
- Attach arbitrary non-identifying metadata to any objects, in a key-value format.
## Usage
- Store build/release IDs, PR numbers, git branch, etc.
- Phone/pager numbers of people responsible, or directory entries specifying where such information can be found.
- Pointers to logging, monitoring, analytics, audit repositories, debugging tools, etc.
- Ingress controller information.
- Deployment state and revision information.
```YAML
apiVersion: apps/v1  
kind: Deployment  
metadata:  
  name: webserver  
  annotations:  
    description: Deployment based PoC dates 2nd Mar'2022  
...
```