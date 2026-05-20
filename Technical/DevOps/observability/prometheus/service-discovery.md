# Service Discovery
## Definition
- Service discovery allows Prometheus to Prometheus to populate a list of endpoints to scrape that can get dynamically updated as new endpoints get created or destroyed.
## Technique
### File
- A list of jobs/targets can be imported from a file.
- This allows you to integrate with service discovery systems which Prometheus doesn't support out of the box.
- Supports json and yaml files. 
### EC2 service discovery
- Prometheus begins collecting extensive metadata from your EC2 instances.
- By default, Prometheus uses the private IP as the instance label because it is typically deployed close to its targets within the same cloud environment. If needed, you can also access the public IP via metadata labels, which is useful when some EC2 instances lack a public IP address.
- You need to to create an IAM user to specifically for Prometheus to scrape the data.
```yaml
scrape_configs:
  - job_name: EC2
    ec2_sd_configs:
      - region: <region>
        access_key: <access key>
        secret_key: <secret key>
```
## Re Labeling
- Enables you to classify and filter targets and metrics by rewriting their label sets.
- Relabeling is especially useful when you have service discovery that identifies multiple targets, but you only want to scrape those in a specific environment (e.g., production).
- With relabeling, you can filter out unwanted targets, rename or drop labels, and even modify label values before or after scraping.
### Options
- `relabel_configs`:
    - Executes before a scrape
    - Has access only to the labels provided by service discovery.
- `metric_relabel_configs`:
    - Executes after a scrape
    - Has access to all collected metrics and labels.
### Actions
- `keep`: only keep the specified labels an