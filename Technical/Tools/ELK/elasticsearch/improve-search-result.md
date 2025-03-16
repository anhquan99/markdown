# Improve Search Result
## Proximity searches
- With `match_phrase`, the value must match the order of the phrase. Using `slop` can increase the distance of the terms, or move the terms around - edit distance.
```http
GET /proximity/_search
{
  "query": {
    "match_phrase": {
      "title": {
        "query": "spicy sauce",
        "slop": 1
      }
    }
  }
}
```
- Boost relevance score by using `bool` with `must` and `should` interchange.
```http
GET /proximity/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": {
              "query": "spicy sauce"
            }
          }
        }
      ],
      "should": [
        {
          "match_phrase": {
            "title": {
              "query": "spicy sauce",
              "slop": 5
            }
          }
        }
      ]
    }
  }
}
```
## Fuzz match query
- Fuzzing the correction of the ES for typo.
- Example: the value is `bar` but use type `baz` the ES will know the typo and show result with `bar` value.
```http
GET /products/_search
{
  "query": {
    "fuzzy": {
      "name": {
        "value": "l0bster",
        "fuzziness": "auto"
      }
    }
  }
}
```
## Synonyms
- TBD
## Highlight matches fields
- TBD
## Stemming
- TBD