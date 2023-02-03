## What is GraphQL?
- GraphQL is a query language for your API, and a server-side runtime for executing queries using a type system you define for your data. GraphQL isn't tied to any specific database or storage engine and is instead backed by your exising code and data.
## Queries and Mutations
- Fields: what you want to see in the result when a query is return.
````js
// query for hero name and their friends name. Fields also refer to an object, with GraphQl queries can traverse related objects and their fields, letting client fetch lots of related data in one request, instead of serveral roundtrips as one would need in a classic REST architecture.
#Query
{
	hero{
		name
		# Query can have comments
		friends {
			name
		}
	}
}
#Result
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
````
- Arguments: specific data queries. ^eb8020
````js
#Query
{
  human(id: "1000") {
    name
    height
  }
}
#Result
{
  "data": {
    "human": {
      "name": "Luke Skywalker",
      "height": 1.72
    }
  }
}
````
- Aliases: rename the result fields to anything you want
````js
#Query
{
  empireHero: hero(episode: EMPIRE) {
    name
  }
}
#Result
{
  "data": {
    "empireHero": {
      "name": "Luke Skywalker"
    }
  }
}
````
- Fragments: let you construct sets of fields, and then include them in queries where you don't need to repeat those fields again. Fragments can take [[GraphQL^]]
````js
#Query
{
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

fragment comparisonFields on Character {
  name
  appearsIn
}
#Result
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ]
    },
    "rightComparison": {
      "name": "R2-D2",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ]
    }
  }
}
````
- Operation name: the default operation name is `query` where there are `mutaion` and `subcription` operation. Every query should have the operartion name for debugging and logging.
- Variable: Like [[GraphQL#^eb8020 | argument]], query could be passed a variable.
```js
#Query
query HeroNameAndFriends($episode: Episode) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
#Variable
{
  "episode": "JEDI"
}
#Result
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

