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
## Queries
### Explicit mappings
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
### 