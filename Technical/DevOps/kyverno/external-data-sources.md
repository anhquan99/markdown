# External Data Sources
## Variables from ConfigMaps
- Kyverno supports using a ConfigMap as a data source for variables.
- When a policy referencing a ConfigMap resource is evaluated, the ConfigMap data is checked at that time ensuring that references to the ConfigMap are always dynamic.
- Should the ConfigMap be updated, subsequent policy lookups will pick up the latest data at that point.
- Look up ConfigMap values: `{{ <context-name>.data.<key-name> }}`.
- Use the labels `cache.kyverno.io/enable` to true to use caching.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: some-config-map
  namespace: some-namespace
data:
  env: production
---
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: cm-variable-example
  annotations:
    pod-policies.kyverno.io/autogen-controllers: DaemonSet,Deployment,StatefulSet
spec:
  rules:
    - name: example-configmap-lookup
      match:
        any:
          - resources:
              kinds:
                - Pod
      context:
        - name: dictionary
          configMap:
            name: some-config-map
            namespace: some-namespace
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              my-environment-name: '{{dictionary.data.env}}'
```
- Use JMESPath filter to handle ConfigMap array values.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: roles-dictionary
  namespace: default
data:
  allowed-roles: '["cluster-admin", "cluster-operator", "tenant-admin"]'
---
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: cm-array-example
spec:
  background: false
  rules:
    - name: validate-role-annotation
      context:
        - name: roles-dictionary
          configMap:
            name: roles-dictionary
            namespace: default
      match:
        any:
          - resources:
              kinds:
                - Deployment
      validate:
        failureAction: Enforce
        message: 'The role {{ request.object.metadata.annotations.role }} is not in the allowed list of roles: {{ "roles-dictionary".data."allowed-roles" }}.'
        deny:
          conditions:
            any:
              - key: '{{ request.object.metadata.annotations.role }}'
                operator: AnyNotIn
                value: '{{ "roles-dictionary".data."allowed-roles" | parse_json(@) }}'
```
## Variables from Kubernetes API Server Calls
- Kyverno uses k8s API to get resource data and using JMESPath to parse result.
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: limits
spec:
  rules:
    - name: limit-lb-svc
      match:
        any:
          - resources:
              kinds:
                - Service
              operations:
                - CREATE
      context:
        - name: serviceCount
          apiCall:
            urlPath: '/api/v1/namespaces/{{ request.namespace }}/services'
            jmesPath: "items[?spec.type == 'LoadBalancer'] | length(@)"
      validate:
        failureAction: Enforce
        message: 'Only one LoadBalancer service is allowed per namespace'
        deny:
          conditions:
            any:
              - key: '{{ serviceCount }}'
                operator: GreaterThan
                value: 1
```
### Construct k8s URL paths
- `/apis/{GROUP}/{VERSION}/{RESOURCETYPE}`: get a collection of resources
- `/apis/{GROUP}/{VERSION}/{RESOURCETYPE}/{NAME}`: get a resource
- `/apis/{GROUP}/{VERSION}/namespaces/{NAMESPACE}/{RESOURCETYPE}`: get a collection of resources in the namespace
- `/apis/{GROUP}/{VERSION}/namespaces/{NAMESPACE}/{RESOURCETYPE}/{NAME}`: get a resource in a namespace
- Use `kubectl api-resources` to get k8s API group
- Use `kubectl api-versions` to get version of k8s API
### Advanced query
- Use the `labelSelect` or the `fieldSelect` in the URL path increase the performance to the Kyverno.
## Global context
- Global Context allows users to cache Kubernetes resources or the results of external API calls for later reference within policies.
- It provides a mechanism to efficiently retrieve and utilize data across policies, enhancing flexibility and performance in policy enforcement.
- This new Global Context ability joins the existing ConfigMap caching ability.
### K8s resources
- Configured with `kubernetesResource`, best for live list of certain resource type.
```yaml
apiVersion: kyverno.io/v2alpha1
kind: GlobalContextEntry
metadata:
  name: deployments
spec:
  kubernetesResource:
    group: apps
    version: v1
    resource: deployments
    namespace: fitness
---
context:
  - name: deployments
    globalReference:
      name: deployments
```
### API call
- Configured with `apiCall`, best for caching a specific subset of resources.
- Refreshes on a schedule via `refreshInterval`.
```yaml
apiVersion: kyverno.io/v2alpha1
kind: GlobalContextEntry
metadata:
  name: deployments
spec:
  apiCall:
    urlPath: '/apis/apps/v1/namespaces/fitness/deployments?labelSelector=app=blue'
    refreshInterval: 10s
```
- API call from external services:
```yaml
apiVersion: kyverno.io/v2alpha1
kind: GlobalContextEntry
metadata:
  name: redisdata
spec:
  apiCall:
    method: GET
    refreshInterval: 1m
    service:
      url: https://redis.myns.svc:6379
      caBundle: |-
        -----BEGIN CERTIFICATE-----
        -----END CERTIFICATE-----
---
context:
  - name: location
    globalReference:
      name: redisdata
      jmesPath: address.city
```
## Variables from Image Registries
- A context can also be used to store metadata on an OCI image by using the `imageRegistry` context type.
- By using this external data source, a Kyverno policy can make decisions based on details of the container image that occurs as part of an incoming resource.
```yaml
context:
  - name: imageData
    imageRegistry:
      reference: 'ghcr.io/kyverno/kyverno'
---
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: imageref-demo
spec:
  rules:
    - name: no-root-images
      match:
        any:
          - resources:
              kinds:
                - Pod
              operations:
                - CREATE
                - UPDATE
      validate:
        failureAction: Enforce
        message: 'Images run as root are not allowed.'
        foreach:
          - list: 'request.object.spec.containers'
            context:
              - name: imageData
                imageRegistry:
                  reference: '{{ element.image }}'
            deny:
              conditions:
                any:
                  - key: "{{ imageData.configData.config.User || ''}}"
                    operator: Equals
                    value: ''
```