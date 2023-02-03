- <mark style="background: #FF5582A6;">Fields:</mark> what you want to see in the result when a query is return.

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

- <mark style="background: #FF5582A6;">Arguments:</mark> specific data queries. ^eb8020

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

- <mark style="background: #FF5582A6;">Aliases:</mark> rename the result fields to anything you want.

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

- <mark style="background: #FF5582A6;">Fragments:</mark> let you construct sets of fields, and then include them in queries where you don't need to repeat those fields again. Fragments can take [[Query and Mutation#^eb8020|arguments]] ^a7289c

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

- <mark style="background: #FF5582A6;">Operation name:</mark> the default operation name is `query` where there are `mutaion` and `subcription` operation. Every query should have the operartion name for debugging and logging.
- <mark style="background: #FF5582A6;">Variable:</mark> Like [[Query and Mutation#^a7289c|fragments]], query could be passed a variable.

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

- <mark style="background: #FF5582A6;">Directives:</mark> is a function to query with conditions.

- <mark style="background: #FF5582A6;">Mutation: </mark> a request to modify data. While query fields are executed in parallel, mutation fields run in series, one after others.

- <mark style="background: #FF5582A6;">Inline Fragments</mark>

- <mark style="background: #FF5582A6;">Meta fields</mark>