# Secrets
- Allowing us to encode the sensitive information before sharing it.
- With Secrets, we can share sensitive information like passwords, tokens, or keys in the form of key-value pairs, similar to ConfigMaps; thus, we can control how the information in a Secret is used, reducing the risk for accidental exposures.
- In Deployments or other resources, the Secret object is _referenced_, without exposing its content.
- It is important to keep in mind that by default, the Secret data is stored as plain text inside `etcd`, therefore administrators must limit access to the API server and `etcd`. However, Secret data can be encrypted at rest while it is stored in `etcd`, but this feature needs to be enabled at the API server level.
## Create secret from literal values
```bash
## Create
$ kubectl create secret generic my-password \  
  --from-literal=password=mysqlpassword
  
## Get
$ kubectl get secret my-password

## Describle
$ kubectl describe secret my-password
```
## Create a secret from a definition manifest
- With `data`, the string content will not be encoded
```YAML
apiVersion: v1  
kind: Secret  
metadata:  
  name: my-password  
type: Opaque  
data:  
  password: bXlzcWxwYXNzd29yZAo=
```
- With `stringData`, encoded the string
```YAML
apiVersion: v1  
kind: Secret  
metadata:  
  name: my-password  
type: Opaque  
stringData:  
  password: mysqlpassword
```
- Create command
```bash
$ kubectl create -f mypass.yaml
```
## Create a secret from a file
```bash
## create a file with name password.txt

## create secret from password.txt
$ kubectl create secret generic my-file-password \  
  --from-file=password.txt
```
## Use secret inside Pods as env variables
```YAML
....  
spec:  
  containers:  
  - image: wordpress:4.7.3-apache  
    name: wordpress  
    env:  
    - name: WORDPRESS_DB_PASSWORD  
      valueFrom:  
        secretKeyRef:  
          name: my-password  
          key: password  
....
```
## Secret as volume
```YAML
....  
spec:  
  containers:  
  - image: wordpress:4.7.3-apache  
    name: wordpress  
    volumeMounts:  
    - name: secret-volume  
      mountPath: "/etc/secret-data"  
      readOnly: true  
  volumes:  
  - name: secret-volume  
    secret:  
      secretName: my-password  
....
```