In Kubernetes, a **resource** is an API endpoint which stores a collection of API objects. For example, a Pod resource contains all the Pod objects.

Although in most cases existing Kubernetes resources are sufficient to fulfill our requirements, we can also create new resources using **custom resources**. With custom resources, we don't have to modify the Kubernetes source.

Custom resources are dynamic in nature, and they can appear and disappear in an already running cluster at any time.

To make a resource declarative, we must create and install a **custom controller**, which can interpret the resource structure and perform the required actions. Custom controllers can be deployed and managed in an already running cluster. 

There are two ways to add custom resources:

- **[Custom Resource Definitions (CRDs)](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)**  
    This is the easiest way to add custom resources and it does not require any programming knowledge. However, building the custom controller would require some programming. 
- **[API Aggregation](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/apiserver-aggregation/)**  
    For more fine-grained control, we can write API Aggregators. They are subordinate API services which sit behind the primary API Server. The primary API Server acts as a proxy for all incoming API requests - it handles the ones based on its capabilities and proxies over the other requests meant for the subordinate API services.