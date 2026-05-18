# Architecture
## Components
- Retrieval: scapes metric data.
- Time series database (TSDB): store metric data.
- HTTP server: accept PromQL query.
- Exporter: collect metric and expose them in a format Prometheus expects.
- Short lived jobs: in the batch workload, jobs that not compatible with pull based model. It needs to push data to Prometheus through Push Gateway.
- Service discovery: providing list of targets for Prometheus to scape.
- Alert manager: sends notification.

```ad-note
Prometheus follows a pull based model, which mean it will pull data from target and it needs to have a list of targets it should scrape.
```
![](/Image/image-37.png)
## Collect metrics
- Prometheus sends http request to /metrics endpoint of each target, but it can be configured with other endpoints as well.
- The exporter must be installed in the system in order to collect metrics and expose them in a format Prometheus expects.
## Scarping models
### Benefit of pull based
- Easier to tell if a target is down while in the push based system it is hard to know if it's down.
- Push based systems could potentially overload metrics server.
- Pull based systems have a definitive list of targets to monitor, creating a central source of truth.
### Use case of push based
- Event based system.
- Short lived jobs, as they may end before it can be pulled.