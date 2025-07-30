# ConfigMaps
- Decouple the configuration details from the container image.
- Using ConfigMaps, we pass configuration data as key-value pairs, which are consumed by Pods or any other system components and controllers, in the form of environment variables, sets of commands and arguments, or volumes.
- We can create ConfigMaps from literal values, from configuration files, from one or more files or directories.
## Create configMap from literal value
```bash
# Create config map from liter value
$ kubectl create configmap my-config \  
  --from-literal=key1=value1 \  
  --from-literal=key2=value2

# Get config map
$ kubectl get configmaps my-config -o yaml
```
## Create configMap from definition Manifest
```YAML
# Example yaml file
apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: customer1  
data:  
  TEXT1: Customer1_Company  
  TEXT2: Welcomes You  
  COMPANY: Customer1 Company Technology Pct. Ltd.
```
- Create config map
```bash
$ kubectl create -f customer1-configmap.yaml
```
## Create configMap from file
- Example of create config map from file
- Create file with name `permission-reset.properties`:
```
permission=read-only  
allowed="true"  
resetCount=3
```
- Execute create command:
```bash
$ kubectl create configmap permission-config \  
  --from-file=<path/to/>permission-reset.properties
```
## Use ConfigMaps inside Pods as env variables
- Single config map:
```YAML
...  
  containers:  
  - name: myapp-full-container  
    image: myapp  
    envFrom:  
    - configMapRef:  
      name: full-config-map  
...
```
- Multiple config maps:
```YAML
...  
  containers:  
  - name: myapp-specific-container  
    image: myapp  
    env:  
    - name: SPECIFIC_ENV_VAR1  
      valueFrom:  
        configMapKeyRef:  
          name: config-map-1  
          key: SPECIFIC_DATA  
    - name: SPECIFIC_ENV_VAR2  
      valueFrom:  
        configMapKeyRef:  
          name: config-map-2  
          key: SPECIFIC_INFO  
...
```
## ConfigMap as volume
```YAML
...  
  containers:  
  - name: myapp-vol-container  
    image: myapp  
    volumeMounts:  
    - name: config-volume  
      mountPath: /etc/config  
  volumes:  
  - name: config-volume  
    configMap:  
      name: vol-config-map  
...
```