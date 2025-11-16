# DeamonSets
- Resemble [[replicaSets|replica sets]] and [[deployments]] operators when managing multiple Pod replicas and application updates, but DeamonSets present a distinct feature that enforces a single Pod replica to be placed per Node, on all the Node. **The ReplicaSet and Deployment by default have no control over the scheduling and placement of multiple Pod replicas on the same node**.
- Commonly used in cases when we need to collect monitoring data from all Nodes, or to run a storage, networking, or proxy deamons on all Nodes, to ensure that we have a specific type of Pod running on all Nodes at all times.
- Whenever a Node is added to the cluster, a Pod from a given DeamonSet is automatically placed on it. When any one Node crashed or it is removed from the cluster, the respective DeamonSet operated Pods are garbage collected, If a DeamonSet is deleted all Pod replicas it created are deleted as well.
```YAML
apiVersion: apps/v1  
kind: DaemonSet  
metadata:  
  name: fluentd-agent  
  namespace: kube-system  
  labels:  
    k8s-app: fluentd-agent  
spec:  
  selector:  
    matchLabels:  
      k8s-app: fluentd-agent  
  template:  
    metadata:  
      labels:  
        k8s-app: fluentd-agent  
    spec:  
      containers:  
      - name: fluentd-agent  
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
```