# Deployments
- Deployment objects provide declarative updates to Pods and ReplicaSets.
- As a part of the control plane node's controller manager, it ensures that the current state always matches the desired state of our running containerized application.
-  Allows for seamless application updates and rollbacks, known as the default **RollingUpdate** strategy, through **rollouts** and **rollbacks**, and it directly manages its ReplicaSets for application scaling. It also supports a disruptive, less popular update strategy, known as **Recreate**.
```YAML
apiVersion: apps/v1  
kind: Deployment  
metadata:  
  name: nginx-deployment  
  labels:  
    app: nginx-deployment  
spec:  
  replicas: 3  
  selector:  
    matchLabels:  
      app: nginx-deployment  
  template:  
    metadata:  
      labels:  
        app: nginx-deployment  
    spec:  
      containers:  
      - name: nginx  
        image: nginx:1.20.2  
        ports:  
        - containerPort: 80
```
- `apiVersion`: is required, specifies the API endpoint on the API server which we want to connect to, it must match an existing version for the object type defined.
- `kind`: required.
- `spec`: marks the beginning of the block defining the desired state of the object.
- The Pod created using the Pod template defined in `spec.template`.
- A nested object, such as Pod being part of a Deployment, retain `metadata` and `spec` and loses its own `apiVersion` and `kind` - both replaced by `template`.