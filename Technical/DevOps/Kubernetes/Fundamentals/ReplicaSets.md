- The next-generation of [[Object#ReplicationControllers|ReplicationController]], as it implements the replication and self-healing aspects of the ReplicationController, supports equal and set-based selector where ReplicationController only support equal selector.
- When a single instance of an application is running there is always the risk of the application instance crashing unexpectedly, or the entire server hosting the application crashing. If relying only on a single application instance, such a crash could adversely impact other applications, services, or clients. To avoid such possible failures, we can run in parallel multiple instances of the application, hence achieving high availability. The lifecycle of the application defined by a Pod will be overseen by a controller - the ReplicaSet. With the help of the ReplicaSet, we can scale the number of Pods running a specific application container image. Scaling can be accomplished manually or through the use of an [autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).
- ReplicaSets can be used independently as Pod controllers but they only offer a limited set of features. A set of complementary features are provided by Deployments, the recommended controllers for the orchestration of Pods. There is no need to manage ReplicaSets and Pods separately, the Deployment will manage them on our behalf.
  ![[Pasted image 20240327140807.png]]
```YAML
apiVersion: apps/v1  
kind: ReplicaSet  
metadata:  
  name: frontend  
  labels:  
    app: guestbook  
    tier: frontend  
spec:  
  replicas: 3  
  selector:  
    matchLabels:  
      app: guestbook  
  template:  
    metadata:  
      labels:  
        app: guestbook  
    spec:  
      containers:  
      - name: php-redis  
        image: gcr.io/google_samples/gb-frontend:v3
```
