- With [[Services]], routing rules are associated with a given Service. They exist for as long as the Service exists, and there are many rules because there are many Services in the cluster. If we can somehow decouple the routing rules from the application and centralize the rules management, we can then update our application without worrying about its external access. This can be done using the **Ingress** resource.
- _"An Ingress is a collection of rules that allow inbound connections to reach the cluster Services"._
- Ingress configures a Layer 7 HTTP/HTTPS load balancer for Services and provides:
	- TLS
	- Name-based virtual hosting
	- Fanout routing
	- Load balancing
	- Custom rules
![[Pasted image 20240328131456.png]]
- With Ingress, users do not connect directly to a Service. Users reach the Ingress endpoint, and, from there, the request is forwarded to the desired Service.
# Ingress controller
- An [Ingress Controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) is an application watching the Control Plane Node's API server for changes in the Ingress resources and updates the Layer 7 Load Balancer accordingly. Ingress Controllers are also known as Controllers, Ingress Proxy, Service Proxy, Revers Proxy, etc. Kubernetes supports an array of Ingress Controllers, and, if needed, we can also build our own
# Deploy an ingress resource
```bash
$ kubectl create -f virtual-host-ingress.yaml
```
# Access services using ingress
```bash
$ sudo vim /etc/hosts
```
# Example of name-based virtual hosting:
- User requests to both `blue.example.com` and `green.example.com` would go to the same Ingress endpoint, and, from there, they would be forwarded to `webserver-blue-svc`, and `webserver-green-svc`, respectively.
```YAML
apiVersion: networking.k8s.io/v1   
kind: Ingress  
metadata:  
  annotations:  
    kubernetes.io/ingress.class: "nginx"  
  name: virtual-host-ingress  
  namespace: default  
spec:  
  rules:  
  - host: blue.example.com  
    http:  
      paths:  
      - backend:  
          service:  
            name: webserver-blue-svc  
            port:  
              number: 80  
        path: /  
        pathType: ImplementationSpecific  
  - host: green.example.com  
    http:  
      paths:  
      - backend:  
          service:  
            name: webserver-green-svc  
            port:  
              number: 80  
        path: /  
        pathType: ImplementationSpecific
```
![[Pasted image 20240328131624.png]]
# Example of fanout
- The Ingress resource does not do any request forwarding by itself, it merely accepts the definitions of traffic routing rules. The ingress is fulfilled by an Ingress Controller, which is a reverse proxy responsible for traffic routing based on rules defined in the Ingress resource.
```YAML
apiVersion: networking.k8s.io/v1  
kind: Ingress  
metadata:  
  annotations:  
    kubernetes.io/ingress.class: "nginx"  
  name: fan-out-ingress  
  namespace: default  
spec:  
  rules:  
  - host: example.com  
    http:  
      paths:  
      - path: /blue  
        backend:  
          service:  
            name: webserver-blue-svc  
            port:  
              number: 80  
        pathType: ImplementationSpecific  
      - path: /green  
        backend:  
          service:  
            name: webserver-green-svc  
            port:  
              number: 80  
        pathType: ImplementationSpecific
```
![[Pasted image 20240328132049.png]]