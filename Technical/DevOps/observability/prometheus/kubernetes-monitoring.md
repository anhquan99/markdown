# Kubernetes Monitoring
## Intension
- Deploy as close to targets as possible.
### Monitor
- Applications running on k8s infra
- Monitor k8s cluster:
	- Control-plane components (`api-server, coredns, kube-scheduler`)
	- `kubelet` (cAdvisor): exposing container metrics
	- `kube-state-metrics`: cluster level metrics (deployments, pods metrics). By default, k8s does not expose metrics so we have to install the `kube-state-metrics` to get metrics from the cluster
	- `node-exporter`: run on all nodes for host related metrics (cpu, memory, network)
## Service monitors CRD
- Service monitors define a set of targets for Prometheus to monitor and scrape.
- Allow you to avoid config Prometheus directly and give you a declarative k8s syntax to define targets.
```yaml
# service.yml
apiVersion: v1
kind: Service
metadata:
  name: api-service
  labels:
    job: node-api
    app: service-api
spec:
  type: ClusterIP
  selector:
    app: api
  ports:
    - name: web
      protocol: TCP
      port: 3000
      targetPort: 3000
---
# Service-monitor.yml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: api-service-monitor
  labels:
    release: prometheus
    app: prometheus
spec:
  jobLabel: job
  endpoints:
    - interval: 30s
      port: web
      path: /swagger-stats/metrics
  selector:
    matchLabels:
      app: service-api
```
- This instructs Prometheus to include any ServiceMonitor with the label `release: prometheus` in its target list. Ensure that your ServiceMonitor resource carries this label for successful integration.
```yaml
# kubectl get prometheuses.monitoring.coreos.com -o yaml
serviceMonitorNamespaceSelector: {}
serviceMonitorSelector:
  matchLabels:
    release: prometheus
```