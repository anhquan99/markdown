# LINQ
- A query is not executed until you iterate over the query variable.
- # 3 parts of a Query operation
	1. ## Obtain the data source
		- The first step is to specify the data source. The `from` clause comes first in order to introduce the data srouce `customers` and the range variable `cust`
		  ``````c#
		  var query = from cust in customers
					  select cust;
		  ``````
		  - The range variable is like iteration variable in a `foreach` loop except that no actual iteration occurs in a query expression. Additional range variables can be introduced by [[LINQ#let clause | let clause]] 
		  - For non-generic data sources such as ArrayList, the range variable must be explicitly type
		    ``````c#
		    var query = from Student s in students ...
		    ``````
	2. ## Create the query
		- ### Filtering
			- Apply filter in the form of a Boolean expression by using `where`
		- ### Ordering
			- Sort the returned data by using `orderby`
			  ``````c#
			  orderby element.property ascending
			  ``````
		  - ### Grouping
			  - `group...by...`
			  - Enables you to group your results based on a key that you specify. Return collection of collections where the all element in inner colletions have the same key.
		  - ### Joining
			  - 
	1.  ## Execute the query
- With LINQ to SQL. First, you create an object-relational mapping. Then you write your queries agains the object, and at run-time LINQ to SQL handles the communication with the database
- To force immediate execution of any query and cache its results, you can call  `ToList()` or `ToArray()` methods 
- Deferred execution: Delay the execution query
 
# Standard query operators
- # Standard query operators are the methods that form the LINQ pattern. Most of the methods operate on sequences, where a sequence is an object whose type implements the `IEnumerable<T>` or `IQueryable<T>` interface
	- # `IEnumerable<T>`
		- When query from a database, `IEnumerable` execute a select query on the server side, load data in-memory on a client side and then filter data
		- Suitable for LINQ to Object and LINQ to XML queries
		-  Does not support custom query, lazy loading hence not suitable for pagin like scenarios
	- # `IQeuryable<T>`
		- When query from a database, `IQueryable` execute the select query on the server side with al filters
		- Suitable for **LINQ to SQL** queries
		- Support custom query, lazy loading
		- Extension methods support by IQueryable takes expression object means expression tree
	- Depend on the result whether the query return a singleton value or sequence of values. 
		- Query returns a singleton value will be executed immediately
		- Query returns sequence of value, depend on datetype of the result `IEnumerable` or `IQueryable` will deferred the result
	- 2 types of syntax
		- # Query expression 
		``````c#
		var query = from word in words
					group word.ToUpper() by word.Length into gr
					orderby gr.Key
					select new {Length = gr.Key, Words = gr}
		``````
		- # Method-based
		  ``````c#
		  var query = words.GroupBy(w => w.Length, w => ToUpper())
					  .Select(g => new {Length = g.Key, Words = g})
					  .OrderBy(x => x.Length);
		  ``````


# `let` clause
- In query expression, it is sometimes useful to sotre the result of a sub-expresison in order to use it in subsequent clauses. You can do this by using `let` clause
  ``````c#
  var query = from  sentence in strings
			  let words = sentence.Split(' ')
			  from word in words
			  let w = word.ToLower()
			  where w[0] == 'a' || w[0] == 'e'
					|| w[0] == 'i' || w[0] == 'o'
				select word;
  ``````
  