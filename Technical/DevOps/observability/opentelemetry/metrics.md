# Metrics
## Definition
- A **metric** is a **measurement** of a service captured at runtime. The moment of capturing a measurement is known as a **metric event**, which consists not only of the measurement itself, but also the time at which it was captured and associated metadata.
- Application and request metrics are important indicators of availability and performance. Custom metrics can provide insights into how availability indicators impact user experience or the business. Collected data can be used to alert of an out age or trigger scheduling decisions to scale up a deployment automatically upon high demand.
## Data model

| Attributes   | Definition                                      | Why it matters                                | Example                                          |
| ------------ | ----------------------------------------------- | --------------------------------------------- | ------------------------------------------------ |
| Monotonicity | Whether the value can only increase of decrease | Rate calculation, reset detection, gauge      | count: (monotonic)<br>cpu usage: (non monotonic) |
| Temporality  | Value is total (cumulative) or change (delta)   | Affects aggregation and backend compatibility | Cumulative: total_errors<br>Delta: new_signups   |
| Aggregation  | how raw values are summarised or grouped        | Enables filtering, grouping, breakdowns       | Histogram for latency, sum for load              |
| Dimensions   | Key-value attributes added to each metric       | Enables filtering, grouping, breakdowns       | method="GET", route="/login"                     |
| Cardinality  | Number of unique dimension combinations         | Impacts performance, cost scalability         |                                                  |

### Monotonicity
- Refers to the property of a metric where its value only increases (monotonic) over time, or if it can decrease as well (non-monotonic).
- Helps choose the right instrument: count or gauge.
- Ex: request count, ...
### Temporality
- Defines how a metric's value relates to time whether it represents a total since the start (cumulative) or a change over an interval (delta).
- Backends use it to delta or cumulative to store the data.
- It affects memory usage and data reliability in exporters.
- Ex: queue size, ...
### Aggregation
- The process of combining raw metric data points into a statistical summary.

| Type                  | Description                             |
| --------------------- | --------------------------------------- |
| Sum                   | Total of all values                     |
| Gauge                 | Last sampled value                      |
| Histogram             | Distribution across buckets             |
| Exponential Histogram | Compressed variant for large-range data |
![](/image/Pasted%20image%2020260509173809.png)
- Drives what kind of insights are possible.
#### Histogram
- A metric instrument that capture the **distribution of recorded values**.
##### Bucket in a histogram
- Think of buckets as bins that group values.
- Buckets can be **explicit** (manually defined thresholds) or **exponential** (auto-scaled).
- They help compute **percentiles, histograms, and distribution summaries** efficiently.
### Dimensions
- An attribute associated with the metric.
- Add context to metric data point as key-value pairs.
- Enables filtering, grouping, and dashboards.
- Forms unique time series.
- Without dimensions, you know something happened. With dimensions, you know exactly what happened, where, and you can take targeted action.
### Cardinality
- Refers to the number of unique combinations of metric attributes (dimensions).
- Ex:
	- Low cardinality: status code (200, 400, 500)
	- High cardinality: unique values user_id, request_id, session_id
- Impact cost, memory and performance.
- Affects the ability to query, filter, and visualize effectively.
- Core to diagnosing anomalies and outliers. High cardinality data is essential for root cause analysis, but must be used carefully.