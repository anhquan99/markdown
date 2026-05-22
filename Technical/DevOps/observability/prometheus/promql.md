# PromQL

## Definition

- Short for Prometheus Query Language.
- Used to:
  - Query metrics within Prometheus
  - Build alerting rules to notify administrators

## Data types

- String
- Scalar: a simple numeric floating point value
- Instant vector: set of time series containing a single sample for each time series, all sharing the same timestamp
  - Every combination of metric and unique labels is one time series
  - Returns metrics at single point in time
  - Example of `node_cpu_seconds_total`

| **Time Series**            | **Time Series**                   |           |                   |
| -------------------------- | --------------------------------- | --------- | ----------------- |
| **Metric**                 | **Labels**                        | Value     | Timestamp         |
| **node_cpu_seconds_total** | **{cpu="0", instance="server1"}** | 258277.86 | March 3rd 11:05AM |
| **node_cpu_seconds_total** | **{cpu="1", instance="server1"}** | 448430.21 | March 3rd 11:05AM |
| **node_cpu_seconds_total** | **{cpu="0", instance="server2"}** | 941202.32 | March 3rd 11:05AM |
| **node_cpu_seconds_total** | **{cpu="1", instance="server2"}** | 772838.83 | March 3rd 11:05AM |

- Range vector: set of time series containing a range of data points over time for each time series
  - Example of node_cpu_seconds_total[3m]

| **Time Series**            | **Time Series**                   |           |                   |
| -------------------------- | --------------------------------- | --------- | ----------------- |
| **Metric**                 | **Labels**                        | Value     | Timestamp         |
| **node_cpu_seconds_total** | **{cpu="0", instance="server1"}** | 674478.07 | March 3rd 08:05AM |
|                            |                                   | 674626.76 | March 3rd 08:06AM |
| **node_cpu_seconds_total** | **{cpu="1", instance="server2"}** | 884597.02 | March 3rd 08:05AM |
|                            |                                   | 540071.18 | March 3rd 11:06AM |
|                            |                                   | 944799.49 | March 3rd 08:07AM |

## Selectors

- Select time series data to retrieve.

### Types

- Multiple selectors
  - Ex: `$ node_filesystem_avail_bytes{instance="node1", device!="tmpfs"}`
- Range vector selector: returns all the values for a metric over a period of time
  - Ex: `node_arp_entries{instance="node1"}[2m]`

## Matchers

- Filter time series by their labels.

### Types

- Equality matcher: `=`
  - Ex: `node_filesystem_avail_bytes{instance="node1"}`
- Negative equality matcher: `!=`
  - Ex: `node_filesystem_avail_bytes{instance!="node1"}`
- Regex matcher: `~`
  - Ex: `node_filesystem_avail_bytes{device=~"/dev/sda.*"}`
- Negative regex matcher: `!~`
  - Ex: `node_filesystem_avail_bytes{device=!~"/dev/sda.*"}`

## Modifier

### Offset modifier

- Offset modifier: get historic data use an offset modifier after the label matching.
  - Ex: `node_memory_Active_bytes{instance="node1"} offset 5m`
- To go to the specific point in time use `@timestamp`
  - Ex: `node_memory_Active_bytes{instance="node1"} @1663265188` - September 15th at 6:06 PM
- Combine offset modifier with the `@timestamp`to go back to the specific time and then go back an offset of time. The order not matter.
  - Ex: `node_memory_Active_bytes{instance="node1"} @1663265188 offset 5m` or `node_memory_Active_bytes{instance="node1"} offset 5m @1663265188`
  - Use with range selector: `node_memory_Active_bytes{instance="node1"}[2m] @1663265188 offset 10m`

## Vector matching

- The process aligns and performs operations between 2 instant vectors.
- Samples with exactly the **same labels** get matched together (identical).
- `ignoring` keyword can be used to ignore an labels to ensure there is a match between 2 vectors. The opposite is`on` which used to specify a list of labels to match on.

### Types

- One to one: every element in the vector on the left of the operation tries to find a single matching element on the right.
- Many to one: each vector elements on the **one** side can match with multiple elements on the **many** side.
  - Use `group_right` which tells PromQL that elements from the left side are now matched with multiple elements from the right.
  - The `group-left` is the same but opposite side.

## Aggregation operators

- Allow you to take an instant vector and aggregate its elements, resulting in a new instant vector, with fewer elements.
- Use `by` to choose which labels to aggregate along.
- Use `without` to do the opposite of `by` which mean to exclude the labels in the aggregation.

## Functions

### Math functions

- `ceil()`
- `floor()`

### Date and time functions

- `time()`: get current time
- `Minute()`
- `Hour()`
- `Day_of_week()`
- `Day_of_month()`
- `Month()`
- `Year()`

### Changing type

- `vector()`: take a scalar value and converts it into an instant vector.
- `scalar()`: return the sample value of the single element as a scalar. If the input does not have exactly one element, it returns NaN.

### Sort

- `sort()`
- `sort_desc()`

### Rate

- A plot of a counter metric does not show anything useful. We expect counters to increase overtime.
- Instead we are more interested in the rate at which a counter metric increase. The `rate()` and `irate()` functions provide the per second average rate of change.
  ![](/image/Pasted%20image%2020260519222222.png)

#### `rate()`

- Calculates the per-second average increase over a specified time range. Consider tracking HTTP errors over a 1-minute period:

```shell
$ rate(http_errors[1m])
```

- With a default scrape interval of 15 seconds, a 1 minute window holds four samples. The function aggregates these samples by subtracting the first sample from the last and then dividing by the duration (60 seconds). For example, given data points 1.2, 2.3, 3.1, and 3.3, the computation would be: 1. Difference: 3.3 - 1.2 = 2.1 2. Rate: 2.1 / 60 = 0.035
  ![](/image/Pasted%20image%2020260519222806.png)

#### `irate()`

- In contrast, the `irate()` function considers only the last two data points in the selected time range, providing a more instantaneous rate. For the same HTTP errors metric with a 1-minute window:

```shell
$ irate(http_errors[1m])
```

- Assuming the last two samples are 10.3 and 8.9, the calculation is: 1. Difference: 10.3 - 8.9 = 1.4 2. Rate: 1.4 / 15 ≈ 0.0933
  ![](/image/Pasted%20image%2020260519223009.png)

| `rate`                                                | `irate`                                                   |
| ----------------------------------------------------- | --------------------------------------------------------- |
| Looks at the first and last data point with a range   | Looks at the last 2 data points                           |
| Effectively an average rate over the range            | Instant rate                                              |
| Best used for slow moving counters and alerting rules | Should be used for graphing volatile fast-moving counters |

### Others

- `absent()` and`absent_over_time()`: if at least one sample of the series exists in the given time window, the function returns nothing (empty vector), otherwise it returns 1 for each combination of values.
- `present()` and `present_over_time()`: opposite of `absent_over_time`.

## Subqueries

- Subquery allows you to run an instant query for a given range and resolution. The result of a subquery is a range vector.

```shell
$ <instant_query>[<range>:<resolution>] [offset <duration>]
```

- Let say you have counter metric, and you want to find the max value of the rate over the past 10 mins. You would have to query with something like this:

```shell
$ max_over_time(node_filesystem_avail_bytes[10m])
```

- But it returns error because the `max_over_time` expect range vector. So to overcome the issue, the subquery helps the instant query return range query with a duration

```shell
$ rate(node_cpu_seconds_total[1m])[2m:10s]
0.991777777804683 @1664226130-- sample every 10s
0.991777777804683 @1664226140/
0.991333333304358 @1664226150
0.991111111131807 @1664226160
0.991111111131807 @1664226170
0.991555555556851 @1664226180
0.989555555561764 @1664226190
0.989555555561764 @1664226200
0.989333333334747 @1664226210
0.989333333334747 @1664226220
0.989333333334747 @1664226230
0.991111111131807 @1664226240
```

## Recording rules

- Allow Prometheus to periodically evaluate PromQL expression and store the resulting time series generated by them.
- Helps speed up dashboards.
- Provide aggregated results for use elsewhere.
- All the rules for a specific job should be contained in a single group.

### Record rule naming

- `level:metric_name:operation`
- Level: indicates the aggregation level of the metric by the labels it has.
- Metric name.
- Operations: list of function and operations that have been applied to the metric.

```yaml
- record: job_method_path:htt_errors:rate5m
  expr: sum without(instance) (rate(http_errors{job="api"}[5m]))

- record: job_method:htt_errors:rate5m
  expr: sum without(instance, path) (rate(http_errors{job="api"}[5m]))
```

## Quantiles

- Determine how many values in a distribution are above or below a certain limit.
- Quantiles represent percentiles.

```shell
histogram_quantile(0.95, request_latency_seconds_bucket{instance="192.168.1.168:8000", job="api", method="GET"})
```

## HTTP API

- Run a query through the API send a POST request to `http://prometheus-ip/api/v1/query` then query is passed into the query parameter.
- Use time parameter to query specific time.

```shell
curl prometheus/api/v1/query --data 'query=node_arp_entries{instance="192.168.1.168:9100"}'
```
