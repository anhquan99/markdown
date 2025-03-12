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
```