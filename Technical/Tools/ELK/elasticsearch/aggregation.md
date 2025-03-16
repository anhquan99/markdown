# Aggregation
## Metric aggregations
```http
GET /index_name/_search
{
  "aggs": {
    "total": {
      "sum": {
        "field": "field_name"
      }
    }
  }
}
```
## Bucket aggregations
- Create bucket of document - set of documents instead of calculate.
```http
GET /index_name/_search
{
  "aggs": {
    "status_terms": {
      "terms": {
        "field": "# field",
		"size": 20, # if not specified default is 10
		"missing": "N/A", # for empty value
		"min_doc_count": 0 # minimum document to create a bucket
		"order": { # order bucket key
          "_key": "asc"
        }
      }
    }
  }
}
```
## Aggregation count problem
- Because of the nature of the ES shard, the index is split into multiple shards.
- `doc_count_error_upper_bound` is the maximum number of missing documents.
```ad-example
Let say there are 3 shards and the index is split evenly into those shards. When finding the count top 3 value of a field (max, min, ...) in the index, ES will get each top 3 value of the shards and aggregate them into the final result.
```

- Shards value

|     | Shard A            | Shard B            | Shard C            |
| --- | ------------------ | ------------------ | ------------------ |
| 1   | Product A (50)     | Product A (50)     | Product A (50)     |
| 2   | Product B (40)     | Product B (40)     | Product E (40)     |
| 3   | Product C (30)     | **Product F (30)** | **Product F (30)** |
| 4   | **Product F (20)** | Product C (20)     | Product B (20)     |
| 5   | Product D (10)     | Product E (10)     | Product C (10)     |
- Result

|     | Actual result   | Correct result  |
| --- | --------------- | --------------- |
| 1   | Product A (150) | Product A (150) |
| 2   | Product B (80)  | Product B (100) |
| **3**   | **Product F (60)**  | **Product F (80)**  |
