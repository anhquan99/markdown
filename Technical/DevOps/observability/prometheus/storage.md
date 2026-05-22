# Storage

## Local storage

- Prometheus stores data in a time series database with 2 hours block.
- Use the `--storage.tsdb.path` to customize the local storage path.
- Data in data folder:
  - `chunk`: metrics collected
  - `tombstones`
  - `index`: indexes name and labels for all the time series within 2 hours block
  - `meta.json`: metadata
- Retention time is 15 days, use `--storage.tsdb.retention.time` to customize.
- Use `--storage.tsdb.retention.size` to customize the retention storage.

### Capacity planning

- Each sample is roughly 1–2 bytes.
- Formula: **Needed disk space = Retention time (seconds) x ingested samples per second x Bytes per sample**.

### Scaling storage

- Prometheus by default does not scale storage horizontally to spread data multiple machines, so the given solution is to store the data to a remote storage.
- Tools:
  - Grafana Mimir
  - Thanos

```yaml
remote_write:
  - url: "http://remote-storage-endpoint:9092/api/v1/write"
    write_relabel_configs: # Optional filtering before sending
      - source_labels: [__name__]
        regex: "http_requests_total|node_cpu_seconds_total"
        action: keep

remote_read:
  - url: "http://remote-storage-endpoint:9092/api/v1/read"
```

#### Thanos

- Must disable compaction feature of Prometheus.
  ![](/image/Pasted%20image%2020260521072512.png)

## Scale Prometheus

- Vertical scaling: a Prometheus monitor a set of applications, services.
- Horizontal scaling: using horizontal sharding.

### Federation

- Federation allows a Prometheus server to scrape selected time series from another Prometheus server.
- Hierarchical federation allows Prometheus to scale to environments with tens of data centers and millions of nodes. In this use case, the federation topology resembles a tree, with higher-level Prometheus servers collecting aggregated time series data from a larger number of subordinated servers.
  ![](/image/Pasted%20image%2020260521074416.png)
