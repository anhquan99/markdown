# Cluster Mesh
## Definition
- Cluster Mesh lets multiple Kubernetes clusters behave as a single multi-cluster network.
## Features
- Cross-cluster network connectivity (pods can talk across clusters).
- Cross-cluster load balancing (services can balance across backends in other clusters).
- Shared security controls (apply Kubernetes NetworkPolicies across clusters).
## Prerequisites and requirements
- Matching datapath mode: All clusters should use the same datapath (e.g., encapsulation/tunnel or native routing) to avoid connectivity and routing mismatches.
- Non-overlapping Pod CIDRs: Pods in different clusters must use unique IP ranges to prevent address conflicts.
- Full node-to-node IP connectivity: Nodes across clusters must be able to reach each other (or through a suitable networking fabric) for cross-cluster traffic and service access.
- Unique cluster identifiers: Each cluster needs a unique cluster name and integer ID in Cilium configuration to avoid collisions.
## Configuration
```yaml
# cluster1 cilium configuration (example)
ipam:
  clusterPoolIPv4PodCIDRList: ["11.0.0.0/8"]
  clusterPoolIPv6PodCIDRList: ["fd00::/104"]

cluster:
  name: cluster1
  id: 1

---
# cluster2 cilium configuration (example)
ipam:
  clusterPoolIPv4PodCIDRList: ["12.0.0.0/8"]
  clusterPoolIPv6PodCIDRList: ["fd01::/104"]

cluster:
  name: cluster2
  id: 2
```

```ad-note
- Provide unique IPv4/IPv6 ranges per cluster if IPv6 is enabled.
- Give each cluster a unique name and integer ID.
```
### Enable Cluster Mesh
-  If your environment cannot automatically provision an appropriate LoadBalancer or service type, you can supply a service type explicitly.

```bash
cilium clustermesh enable --context $CLUSTER1 --service-type=LoadBalancer
cilium clustermesh enable --context $CLUSTER2 --service-type=LoadBalancer

# The LoadBalancer EXTERNAL-IP (when provisioned) is used by remote clusters to reach the Cluster Mesh API server.
kubectl get svc -n kube-system

# Connect cluster into a mesh
cilium clustermesh connect --context $CLUSTER1 --destination-context $CLUSTER2

# check mesh status
cilium clustermesh status
```

## KVStoreMesh 
- KVStoreMesh is a design improvement introduced to scale Cluster Mesh.
- Enable by deault by Cilium.
### Original challenges
- Each Cilium agent/operator wrote resources (Services, CiliumNodes, identities, endpoints) into the cluster Kubernetes API.
- The Cluster Mesh API server would watch the Kubernetes API and sync that data into a central etcd.
- Remote agents had to watch many remote etcd instances; at scale this caused high synchronization load, increased latency, and heavy etcd pressure.
![](/image/Pasted%20image%2020260610052210.png)
### KVStoreMesh design
- Each cluster runs a local KVStoreMesh binary and a local etcd instance containing mesh-wide state.
- Cilium agents sync only with the local KV store.
- KVStoreMesh instances synchronize state between clusters, reducing the number of remote endpoints that each agent must watch.

### Benefits
- Reduced overall etcd usage and pressure.
- More balanced load across cluster KV stores.
- Lower impact from agent restarts, workload churn, or new clusters joining the mesh.

### Disable KVStoreMesh
```bash
# Enable Cluster Mesh but disable KVStoreMesh explicitly (compatibility mode)
cilium clustermesh enable --context $CLUSTER1 --service-type=LoadBalancer --kvstore-mesh=false
```
## Global service
- By default, a Kubernetes Service load-balances only to endpoints (Pods) in the same cluster.
- Cilium Cluster Mesh can expose Services across clusters. Enabling a global service allows cross-cluster load balancing and failover.
- To create a global service, deploy identical Service resources (same name, namespace, and ports) in each cluster where the Service should be reachable, and add the global annotation to enable