# Query
```
# Create index
PUT /index_name
{
	"settings": {
		"number_of_shards": 2,
		"number_of_replicas": 2
	}
}

# Delete index
DELETE /index_name

# Index (create) a document
POST /index_name/_doc
{
	# document
}
# Index a document with id
POST /index_name/_doc/id
{
	# document
}

# Get document by id
GET /index_name/_doc/id

# Update document
POST /index_name/_update/id
{
	"doc": {
		# new document
	}
}

# Scripted update
POST /index_name/_update/id
{
	"script": {
		"source": "# document"
	},
	"params": { # optional
		# parameters
	}
}

# Upsert, when no document is found then create a new document with document in upsert
POST /index_name/_update/id
{
	"script": {
		"source": "# document"
	},
	"upsert": {
		# new document
	}
}

# Replace document
PUT /index_name/_doc/id
{
	# document
}

# Delete document
DELETE /index_name/_doc/id

# Update by query
POST /index_name/_update_by_query
{
	"script": {
		"source": "# document"
	},
	"query": {
		# query
	}
}

# Delete by query
POST /index_name/_delete_by_query
{
	"query": {
		# query
	}
}

# Batch process
POST /_bulk
{ "action": {document} }
{ "action": {document} }
{ "action": {document} }

# CURL bulk
curl --cacert ca.crt -u elastic -H "Content-Type:application/x-ndjson" -XPOST https://localhost:9200/products/_bulk --data-binary "@products-bulk.json"
```
## Operation
- Used with scripted update by setting the `ctx.op`.
- `noop`: no operation, cancel operation using with `ctx.op = 'noop`
- `delete`: delete operation.