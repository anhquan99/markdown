# Visualization
## Definition
- Visualization is visually represent Prometheus metrics using graphs, tables, charts, and dashboards.
## Tools
### Expression browser
- Built in web UI that can be used to execute queries and simple graphs.
- Has limited function and should be used for ad-hoc queries and quick debugging.
- Has no ability to build custom dashboards and not for day-to-day monitoring.
- Port: 9090
### Console templates
- Allow you to create your own custom HTML pages using Go templating language.
- Prometheus metrics, queries and charts can be embedded in the templates.
##  Application instrumentation
### Best practices
### Naming
- Follow snake_case - lower case and words separated by `_`.
- The first word of the metric should be the application/library the metric is used for.
- The name of the metric name should provide a description of what the metric is used for. More than one word can be used.
- The unit should always be included in the metric name, so there's no second guessing which unit it's using.
- Make sure to use unprefixed base units like seconds, bytes, and metrics. We don't want to use microseconds or kilobytes.
- `_total, _count, _sum, and _bucket` suffixes are use by various metrics so it's best to avoid adding those yourselves.
### What to instrument
- **Online-serving systems:** required immediate response is expected. Databases and web-servers would fall into this category.
	- Number of queries/requests
	- Number of errors
	- Latency
	- Number of in progress requests
- **Offline processing:** no one is actively waiting for a response. It is usually patch up work and have multiple stages in a pipeline.
	- Amount of queued work
	- Amount of work in progress
	- Rate of processing
- **Batch jobs:** similar to offline-serving systems with the main difference being batch jobs are run on a regular schedule whereas offline-serving systems run continuously. Required a `pushGateway` as it isn't continuously running to be scraped
	- Time processing each stage of job
	- Overall runtime