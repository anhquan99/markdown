# Autoscaling
- Autoscaling can be implemented in a Kubernetes cluster via controllers which periodically adjust the number of running objects based on single, multiple, or custom metrics.
## Types
- **[Horizontal Pod Autoscaler (HPA)](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)**  
    HPA is an algorithm-based controller [API resource](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/autoscaling/horizontal-pod-autoscaler.md#horizontalpodautoscaler-object) which automatically adjusts the number of replicas in a ReplicaSet, Deployment or Replication Controller based on CPU utilization.  Increase resource by adding more pods.

- **[Vertical Pod Autoscaler (VPA)](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler#readme)**  
    VPA automatically sets Container resource requirements (CPU and memory) in a Pod and dynamically adjusts them in runtime, based on historical utilization data, current resource availability and real-time events. Increase resource by evicting a pod to change resource limit.

- **[Cluster Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler#cluster-autoscaler)**  
    Cluster Autoscaler automatically [re-sizes the Kubernetes cluster](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md) when there are insufficient resources available for new Pods expecting to be scheduled or when there are underutilized nodes in the cluster.