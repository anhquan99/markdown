# Analysis Engine
- When indexing document, the ES analyze the text fields/values.
- The result is stored in data structures that are efficient for searching.
- The `_source` object is not used when searching for documents.
- There are built-in analyzers, character filters, tokenizers, and token filters are available or you can build your own.
![[image-10.png]]
## Character filters
- Adds, removes, or changes characters.
- Analyzers contain zero or more character filters.
- Character filters are applied in the order in which they are specified.
- Example (`html_stip`):
	- **Input:** `<h1>Hi</h1>`
	- **Output:** `Hi`
## Tokenizer
- An analyzer contains **one** tokenizer.
- The process of splitting the text into tokens.
- Characters may be stripped as part of the tokenization.
- Example:
	- **Input:** "Hello world"
	- **Output:** \["Hello", "world"]
## Token filter
- Receive the output of the tokenizer as input and then add, remove or modify tokens.
- An analyzer contains zero or more token filters.
- Token filters are applied in the order in which they are specified.
- Example (`lowercase`):
	- **Input:** \["HELLO", "WORLD"]
	- **Output:** \["hello", "world"]
## Analyzer API
```http
# Standard analyzer
POST /_analyzer
{
	"text": "# text",
	"analyzer": "standard" # you can choose other analyzer
}

# explicit analyzer
POST /_analyzer
{
	"text": "# text",
	"char_filter": [],
	"tokenizer": "standard", # you can choose other tokenizer
	"filter": ["lowercase"]
}
```
## Inverted index
-  Value for a text field are analyzed, and the results are stored within an inverted index which mapping between terms and which documents contain them helps ES search fast.
- An index term is the **unit of search**.
- Terms are stored alphabetically for performance reasons.
- Created and maintained by Apache Lucene.
- Inverted index exists within the scope of a text field. Example
```json
{
	"name": "food" // name inverted index 
	"description": "bar" // description invered index
}
```
- The other data types use BKD trees.
![[image-11.png]]
## Mapping
- Define the structure of documents and used to configure how values are indexed.
- Similar to a table's schema in SQL.
- You can use explicit mapping or dynamic mapping, or both interchange.
### Update mappings
- ES mappings can't be changed.
- New filed mappings can be added.
- A few mapping parameters can be updated for existing mappings.
- To update a mapping, the solution is to reindex documents into a new index.
#### Reindexing API
- The data type doesn't reflect how the values are indexed.
- Fields can be removed using filter.
- Documents can be queried or deleted.
- A snapshot is created before reindexing documents.
- A version conflict causes the query to be aborted by default.
- Performs operations in batches.
- Throttling can be configured to limit the performance impact.
```http
POST /_reindex
{
	"source": {
		"index": "index_name",
		"query": {}
		
	},
	"dest": {
		"index": "new_index_name"
	},
	"script": {
		"source": "# script"
	}
}
```
### Data types
- ES store data with data types, not only text.
#### Object
- Used for any JSON object.
- Objects may be nested.
- Objects are flattened.
	- Example: `a.b.c: "text"`
#### Nested
- Similar to the object data type, but maintains object relationships, useful when indexing an array of objects.
- Enable query object independently.
#### Keyword
- Used for exact matching of values.
- Typically used for filtering, aggregations, and sorting.
- For full-text searches, use the `text` data type.
- Analyzed with the keyword analyzer, which is a no-op analyzer
	- Outputs if the unmodified string as a single token
	- This token is then placed into the inverted index.
#### Datetime
-  Specified in 3 ways:
	- String
	- Milliseconds since the epoch
	- Second since the epoch
- Epoch is 1/1/1970
- Custom format supported:
	- Date without time
	- A date with time
	- Millisecond since the epoch
- Default UTC timezone assumed if not specified.
- Date must be formatted according to the ISO 8601 specification.
- ES store datetime internally as milliseconds since the epoch.
- Dates are converted to the UTC timezone.
### Type coercion
- Data types are inspected when indexing documents.
- ES resolves the data type that was first supplied for a given field.
- Search queries use indexed values not `_source`.
- Supplying a floating point for a `int` field will truncate it to an integer.
- Coercion is not used for dynamic mapping.
- Always try to use the correct data type, especially the first time you index a field.
- Coercion can be disabled, and it is enabled by default.
```ad-note
There is no dedicated array data type. Query an array of objects, ES would look for match fields across different objects in the array, leading unexecpted results, to ensure queries match values within the same object, use the nested data type instead of the object data type. By default, arrays of objects are flattened during indexing. Array values must be the same type.
```
### Parameter
#### `format`
- Used to customize the format for `date` fields.
#### `properties`
- Defines nested fields for `object` and `nested` fields.
#### `coerce`
- Used to enable or disable coercion of values.
#### `doc_value`
- `doc_value` is another data structure used by Apache Lucene, optimized for a different data access pattern (aggregation, sorting, ...).
- Disable `doc_value` to save disk space and slightly increase the indexing throughput when you don't need to use aggregation, sorting, or scripting.
- Useful for large indices.
- Must be reindex when change `doc_value`.
#### `norms`
- Normalization factor used for relevance scoring.
- Norms can be disabled to save disk space.
#### `index`
- Used for ES indexes a field.
- Disabling `index` is useful when you don't need to search a field, which helps save disk space and slightly improves indexing throughput and the field can still be used for aggregations.
- Often used for time series data.
#### `null_value`
- `NULL` value cannot be indexed or searched.
- Use this parameter to replace `NULL` values with another value, but the replacement value must be the same as the field, and it's only works for explicit `NULL` values.
#### `copy_to`
- Used to copy multiple field values into a **group field**.
- **Values** are copies, not terms/tokens, and it is not a part of `_source`.
- Example: `first_name` and `last_name` → `full_name`.
### Multiple mappings
- Used for multiple search purpose.
- Example: full-text search and keyword search.
```http
POST /index_name
{
	"mappings": {
		"properties": {
			"field_name": {
				"type": "text",
				"fields": {
					"keyword": { # this field name can be optionally changed
						"type": "keyword"
					}
				}
			}
		}
	}
}
```
### Queries
#### Explicit mappings
```http
PUT /index_name
{
	"mappings": {
		"properties": {
			"fied_name_1": { "type": "data_type"},
			"fied_name_2": { "type": "data_type"},
		}
	}
}
```
#### Retrive mappings
```http
GET  /index_name/_mapping
```
### Index templates
- It is a way to automatically apply settings and mappings on index creation, which works by matching index names against an **index pattern**.
- Typically used for data sets that stored in multiple indices (time series data, ...).
```http
PUT _index_template/template_name
{
  "index_patterns": ["foo-*", "bar-*"],
  "template": {
    "settings": {},
    "mappings": {},
 
  },
  "priority": 1, # this can be changed
}
```
#### Index document with index template
- Auto create index with index template when index document
![Auto create index with index template when index document|402x459](/Image/elk-index-template-auto.excalidraw.png)
- Manual create index with index template
![Manual create index with index template|402x459](/Image/elk-index-template-manual.excalidraw.png)