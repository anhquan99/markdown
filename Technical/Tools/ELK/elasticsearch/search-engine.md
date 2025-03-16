
# Search Engine
## Types
### URL Searches
- Using URL with query string to search data, which uses Apache Lucene's query syntax.
### Query DSL
- Using URL with embed JSON to request body to search data.
- Skipped shards are shards contain no documents that match the request.
## Term level queries
- Used to search structured data for exact values (filtering).
- Term level queries are not analyzed.
- Can be used with data types such as `keyword`, numbers, dates, ...
- Don't use term level queries on `text` fields.
```http
# single search term
GET /index_name/_search
{
  "query": {
    "term": {
      "field.keyword": {
        "value": "# value",
        "case_insensitive": true # default false
      }
    }
  }
}

# multiple search terms
GET /index_name/_search
{
  "query": {
    "terms": {
      "field.keyword": ["foo", "bar"]
    }
  }
}
```
### Other searches
```http
# prefix
GET /index_name/_search
{
  "query": {
    "prefix": {
      "field.keyword": {
        "value": "# value"
      }
    }
  }
}

# wildcards search includes ? - match 1 or more value and * - match 0 or more value 
GET /index_name/_search
{
  "query": {
    "wildcard": {
      "field.keyword": {
        "value": "# value"
      }
    }
  }
}

# regex
GET /index_name/_search
{
  "query": {
    "regexp": {
      "field.keyword": {
        "value": "# regex"
      }
    }
  }
}

# case insensitive
GET /index_name/_search
{
  "query": {
    "prefix": {
      "field.keyword": {
        "value": "# value",
        "case_insensitive": true
      }
    }
  }
}

# Query existing field
# NULL and [] (empty array) are considered as not exist value
GET /index_name/_search
{
  "query": {
    "exists": {
      "field": "# field"
    }
  }
}

# Query existing field
index_name/_search
{
  "query": {
    "bool": {
      "must_not": [
        {
          "exists": {
            "field": "# field"
          }
        }
      ]
    }
  }
}
```
## Range searches
```http
# numberic
GET /index_name/_search
{
  "query": {
    "range": {
      "field": {
        "gt": 1,
        "lt": 5
      }
    }
  }
}

# date with format
GET /index_name/_search
{
  "query": {
    "range": {
      "created": {
        "format": "dd/MM/yyyy",
        "gte": "01/01/2020",
        "lte": "31/01/2020"
      }
    }
  }
}

# UTC offset
GET /index_name/_search
{
  "query": {
    "range": {
      "created": {
        "time_zone": "+01:00",
        "gte": "2020/01/01 01:00:00",
        "lte": "2020/02/01 00:59:59"
      }
    }
  }
}
```
## Full text queries
- Used for searching unstructured text data - not for exact matching.
- Full text queries are analyzed.
- Don't use full text queries on `text` fields.
```http
GET /index_name/_search
{
  "query": {
    "match": {
      "field": "value"
    }
  }
}

# multiple terms
GET /index_name/_search
{
  "query": {
    "match": {
      "field": "foo bar",
      "operator": "and" # if not specified the default is OR operator
    }
  }
}
```
### Relevant scoring
- Because the full text queries are not for exact matching, therefore how well a document matches is now a factor.
- Query results are sorted in descending order by the `_score` metadata field.
- Documents matching term level queries are generally scored 1.0.
### Searching multiple fields
- The `multi_match` query performs full text searches on multiple fields.
- Individual fields can be relevance boosted by modifying the field name.
- `tie_breaker` is used for document gets a relevance boost for each additional field that matches besides the best matching field. If some of the terms appear in the fields with a lower relevance score, this still signals some relevance that might take into account.
```http
GET /index_name/_search
{
  "query": {
    "multi_match": {
      "query": "value",
      "fields": ["foo", "bar"]
    }
  }
}

# Per-field relevance boosting
GET /index_name/_search
{
  "query": {
    "multi_match": {
      "query": "value",
      "fields": ["foo^2", "bar"]
    }
  }
}

# tie breake
GET /index_name/_search
{
  "query": {
    "multi_match": {
      "query": "value 1",
      "fields": ["foo", "bar"]
      "tie_breaker": 0.3
    }
  }
}
```
## Phrase search
- Search with matter of position.
- Terms must appear in the correct order and with no other terms in-between.
```http
GET /index_name/_search
{
  "query": {
    "match_phrase": {
      "name": "foo bar"
    }
  }
}
```
## Compound query - `bool`
### `should`
- If `bool` query only contains `should` clauses, at least one must match.
- Useful if you just want something to match and reward matching documents.
- If a query clause exists for `must`, `must_not`, or `filter`, no `should` clause is required to match.
```http
# should clause must match
GET /index_name/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "term": {
            "field": "foo"
          }
        }
      ]
    }
  }
}

# shoud clauses are optional
GET /index_name/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "field": "foo"
          }
        }
      ]
      "should": [
        {
          "term": {
            "field": "bar"
          }
        }
      ]
    }
  }
}

# minimum should match
# it should match 1 should clause
GET /index_name/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "field": "foo"
          }
        }
      ], 
      "should": [
        {
          "term": {
            "field": "bar"
          }
        },
        {
          "match": {
            "name": "baz"
          }
        }
      ],
      "minimum_should_match": 1
    }
  }
}
```
### `filter`
- Query clause must match, but ignores relevance scores, which leads to improves query performance and query result can be cached and reused.

| Occurrence type | Description                                                                                                                                    |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| must            | Required to match and contribute to relevance scores                                                                                           |
| filter          | Required to match and not contribute to relevance scores, which leads to improves query performance and query result can be cached and reused. |
| must_not        | Must not match and not contribute to relevance scores, which leads to improves query performance and query result can be cached and reused.    |
| should          | Should or must match in some cases and contribute to relevance scores.                                                                         |
## Boosting query
- Increase or decrease relevance scores with boosting query.
```http
# I like foo but not bar
GET /index_name/_search
{
  "query": {
    "boosting": {
      "positive": {
        "term": {
          "field": "food"
        }
      },
      "negative": {
        "term": {
          "field": "bar"
        }
      },
      "negative_boost": 0.5
    }
  }
}
```
## Query array of objects
### Problem
- When indexing arrays of objects, the relationships between values are not maintained.
- Queries can yield **unpredictable** results.
### Solution
- Use nested data type and the nested query,
- Create a new index to update the field mapping and reindex document.
#### Nested inner hits
- Nested inner hits tell use which nested object(s) matched a query, without it we only know that something matched.