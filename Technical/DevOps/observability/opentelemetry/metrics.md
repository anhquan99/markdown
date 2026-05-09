# Metrics
## Definition
- A **metric** is a **measurement** of a service captured at runtime. The moment of capturing a measurement is known as a **metric event**, which consists not only of the measurement itself, but also the time at which it was captured and associated metadata.
- Application and request metrics are important indicators of availability and performance. Custom metrics can provide insights into how availability indicators impact user experience or the business. Collected data can be used to alert of an out age or trigger scheduling decisions to scale up a deployment automatically upon high demand.
## Instrument kind

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
## Data model
- A blueprint showing how OTel organize metric data from collection to export to any backend.
### Foundation
#### Protocol and semantics
- Standardized metric model: defines a consistent protocol and semantic framework for metrics.
- Metric is the same no matter where they come from.
#### Interoperability
- System compatibility: supports importing/exporting from systems like Prometheus and StatD.
#### Smart data handling
- Efficient data transformations: transforms metric data efficiently.
- Metrics can be transformed across time or space, which means aggregating across server or converting delta values into cumulative without losing fidelity.
#### Future-proof design
- Extensible and robust: designed for extensibility.
### Flow of metrics
![](/image/Pasted%20image%2020260509202005.png)
- Existing systems collecting metrics, but they use different format and semantics.
- OpenTelemetry metric data model sit in the middle to standardizes the diverse formats and outputs consistent data to export data to any backend.
#### Transformation
- Unit standardization
- Aggregation temporality
- Dimension enrichment
#### Configurability and cost control
- Spatial reaggregation: simplify attributes, drop unwanted attributes to reduce dimensionality and avoid cardinality explosion
- Temporal reaggreation: roll up time series, convert high-frequency metrics into longer interval, reducing storage while preserving trends
- Delta to cumulative conversion: transform delta metrics into cumulative totals, shifting state management to collectors
- Reliability: reducing noise and ensuring that the metrics you keep are consistent and interpretable across systems.
- Statelessnes: Otel transformations are designed to avoid hidden dependencies on previous states.
## Metrics data journey
### 1. Event model
- Metrics begin as raw observations
- Includes:
	- Instrumentation: applications use APIs to record raw metric events
	- Metric instruments: counters, gauges, ...
	- Attributes and context: event include attributes for filtering and rich analysis
### 2. OTLP stream model
- Transit state
- Metrics transported efficiently via OTLP
```
Raw events → SDK transformation → Data streaming → Export protocol → Send to backend
```
### 3. Timeseries model
- At rest stage
- Metrics stored in timeseries DB
## Metric instrument
- Instruments are defined by a name, type, and optional detail such as units, descriptions, and advisory hints.
### Parameter
- Instrument name
- Kind: determines the behavior, ie: counter, gauge, histogram
- Unit descriptions: provide context and advisory parameters
- Advisory parameter
### Instrument types
- Synchronous: record measurement inline with application logic and carry execution context - context aware
- Asynchronous: use callback triggered during collection, so they report on demand without context
### Instrument kinds
#### Counter instrument
- Monotonic instrument that tracks every-increase total.
#### Async counter instrument
- Report cumulative monotonic values via callbacks only observed.
- Idea for metrics such as CPU time.
#### Histogram instrument
- Capture arbitrary measurement to produce statistical summaries such as percentiles.
#### Gauge instrument
- Records non-addictive measurements, updating when a value changes, such as background noise level in decibels.
#### Async gauge instrument
- Invokes a callback at collection time to record non-addictive snapshots, such as room temperature.
#### UpDownCounter instrument
- Allows both increment and decrement, making it ideal for tracking dynamic counts such as active requests or queue length.
#### Async UpDownCounter instrument
- Report additive values when observed.