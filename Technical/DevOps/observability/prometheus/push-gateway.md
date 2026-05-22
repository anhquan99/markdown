# Push Gateway

## Definition

- Batch jobs only run for a short period of time and then exit which makes it difficult for Prometheus to scrape. So the batch jobs will push metrics to push gateway before it exist.
- Push gateway acts as the middle man between the batch job and the Prometheus server.
  ![](/image/Pasted%20image%2020260520193403.png)

## Send metrics to push gateway

### HTTP

- URL `http://<pushgateway_address>:<port>/metrics/job/<job_name>/<label1>/<value1>/<label2>/<value2>`
  - `<job_name>`
  - `<label>/><value>`: in the url path is used as a grouping key - allows for grouping metrics together to update/delete multiple metrics at once.
- The URL path (job_name + labels) acts as a grouping key.
- All metrics sent to a specific URL path are part of a group.
- Groups allow you to update/delete multiple metrics at once.
- When sending a POST request, only metrics with the same name as the newly pushed metrics are replaced (only applies to metrics in same group).
- When sending a PUT request, all metrics within a specific group get replaced by the new metrics being pushed.
- Delete request will delete all metrics within a group.

### Prometheus client library

- There are 3 functions within a client library to push metrics:
  - `push` = PUT request
  - `pushadd` = POST request
  - `delete` = DELETE request
