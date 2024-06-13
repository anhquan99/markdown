- A query is not executed until you iterate over the query variable.
# 3 parts of a Query operation
1. Obtain the data source
	- The first step is to specify the data source. The `from` clause comes first in order to introduce the data source `customers` and the range variable `cust`
	```c#
	  var query = from cust in customers
				  select cust;
	```
	  - The range variable is like the iteration variable in a `foreach` loop, except that no actual iteration occurs in a query expression. Additional range variables can be introduced by `let`
	  - For non-generic data sources such as ArrayList, the range variable must be explicitly type
	```c#
		var query = from Student s in students ...
	```
	2. Create query
	3. Execute query
- With LINQ to SQL. First, you create an object-relational mapping. Then you write your queries agains the object, and at run-time LINQ to SQL handles the communication with the database
- To force immediate execution of any query and cache its results, you can call  `ToList()` or `ToArray()` methods 
- Deferred execution: Delay the execution query
# Standard query operators
- Standard query operators are the methods that form the LINQ pattern. Most of the methods operate on sequences, where a sequence is an object whose type implements the `IEnumerable<T>` or `IQueryable<T>` interface
## `IEnumerable<T>`
- When query from a database, `IEnumerable` execute a select query on the server side, load data in-memory on a client side and then filter data
- Suitable for LINQ to Object and LINQ to XML queries
-  Does not support custom query, lazy loading hence not suitable for paging like scenarios
## `IQeuryable<T>`
- When query from a database, `IQueryable` execute the select query on the server side with all filters
- Suitable for **LINQ to SQL** queries
- Support custom query, lazy loading
- Extension methods support by `IQueryable` takes expression object means expression tree
- Depend on the result whether the query return a singleton value or sequence of values. 
	- Query returns a singleton value will be executed immediately
	- Query returns sequence of value, depend on data type of the result `IEnumerable` or `IQueryable` will defer the result
- 2 types of syntax:
	- <mark style="background: #BBFABBA6;">Query expression</mark> 
	``````c#
	var query = from word in words
				group word.ToUpper() by word.Length into gr
				orderby gr.Key
				select new {Length = gr.Key, Words = gr}
	``````
	- <mark style="background: #BBFABBA6;">Method-based</mark>
	  ``````c#
	  var query = words.GroupBy(w => w.Length, w => ToUpper())
				  .Select(g => new {Length = g.Key, Words = g})
				  .OrderBy(x => x.Length);
		  ``````
# `Equijoins`
- Performs a join against equality or matching columns values of the associated tables.
- An equal sign `=` is used as comparison operator
- You can perform `equijoins` by using `join` keyword followed by `on`.
# Non `equijoins`
- Like [[LINQ#Equijoins | Equijoins]], non equijoin performs a join with comparision operator instead of the equal sign like `>`, `<`, ... along with conditions.
# Query keyword
## `orderby`
- Sort the returned data by using `orderby`
```c#
  orderby element.property ascending
```
## `group`
### `group...by...`
  - Enables you to group your results based on a key that you specify. Return collection of collections where the all element in inner collection have the same key.
  - **Grouping by string**
  - **Grouping by bool**
    ``````c#
    var query = from student in students
			    group student by student.Scores.Average() > 80;
    ``````
- **Grouping by numeric range**
  ``````c#
      var query = from student in students
			      let avg = (int) student.Scores.Average()
			      group student by (avg/10) into g
			      orderby g.Key
			      select g;
	/* Output: 
	Students with an average between 70 and 80 
		Omelchenko, Svetlana:77.5 
		O'Donnell, Claire:72.25 
		Garcia, Cesar:75.5 
	Students with an average between 80 and 90 
		Garcia, Debra:88.25 
	Students with an average between 90 and 100 
		Mortensen, Sven:93.5 
	*/
  ``````
- **Grouping by composite keys**
```c#
  group person by new {name = person.surname, city = person.city}
```
##  `join`
- Take 2 data source as input. Compare elements in each sequence must either be or contain a property that can be compared to a corresponding property in the other sequence. Using `equals` keyword to compare specified keys. All joins performed by `join` clause are [[LINQ#Equijoins | Equijoins]] 
```c#
	from data_1 from dataset_1
	join data_2 from dataset_2 
	on data_1.property equals data_2.property
	select data_1;
```
- `join` takes which side you choose to take. In this code above it will return data from `dataset_1`. You can merge 2 datasets by using anonymous `new{}`
### Composite key join
```c#
from data_1 from dataset_1
join data_2 from dataset_2 
on new {data_1.property_1, data_1.property_2} 
equals new {data_2.property_1, data_2.property_2}
```
#### `Inner join`
- `Inner join` produces a result set in which each element of the first collection appears one time for every matching element in the second collection. If an element in the first collection has no matching elements, it does not appear in the result set.
#### `Group join`
- A `join` clause with an `into` expression is called a group join.
- If no elements from the right source sequence are found to match an element in the left source, the `join` will produce an empty array for that item. Basically it is an inner-`equijoin` except that the result sequence is organized into groups.
```c#
from data_1 from dataset_1
join data_2 from dataset_2 
on data_1.property equals data_2.property
into dataGroup
```
  - In the query above, the dataGroup will be the collection of each element key in data_1
```C#
var query = from person in people 
			join pet in pets on 
			person equals pet.Owner into gj 
			select new { 
				OwnerName = person.FirstName, 
				Pets = gj 
			};
```
#### Left outer join
- The first step in producing a left outer join of two collections is to perform an inner join. 
- The second step is to inlcude each element of the first (left) collection in the result set, even if that element has no matches in the right collection. By using `DefaultIfEmpty()` .
```c#
 var query = from Owner in people
		  join pet in pets on Owner equals pet.Owner
		  into gj
		  from subpet in gj.DefaultIfEmpty()
		  select new
		  {
			  Owner.FirstName,
			  PetName = subpet?.Name ?? String.Empty
	  };
``` 
- The method returns a collection that contains a single default value if the sequence matching `Pet` objects is empty for any `Person` object, thereby ensuring that each `Person` object is represented in the result collection.
```ad-note
 The default value for reference type is `null` therefor, the example checks for a null reference before accessing each element of each `Pet` collection.
```

#### The equals operator
- `join` clause uses the `equals` keyword for [[LINQ#Equijoins | equijoins]] instead of `==` operator.
- When comparing `null` the result is `false` by using `equals` but `==` results `true`. 
- With `equals`, the left key consumes the outer source sequence, and the right key consumes the inner source. The outer source is only in scope on the left side of `equals` and the inner source sequence is only in scope on the right side.
- #### `from`
	- `from` clause specifies strongly typed:
		-  Data source on which the query will be run
		- A local range variable that represent each element in the source sequence like array
#### Compound from clauses
- An element contain a sequence. For example students where each student contains a list of test scores. To access each element in test scores, we use compound from clauses, using this like using nested for each loop.
```c#
var scoreQuery = from student in students
				from score in student.Scores
					where ...
```
##### `where`
 - `where` clause is a filtering mechanism. A single query expression may contain multiple `where` clauses and a single clause may contain multiple predicate subexpressions.
##### `select`
- `select` clause specifies the typeof values that will be produced when the query is executed. The result is based on the evaluation of all the previous clauses and on any expressions in the `select` clause itself.
- A query expression must terminate with either a `select` clause or a `group` clause
##### `into`
- Used to create temporary identifier to store the results of a `group`, `join` or `select` into a new identifier.
##### `let`
- In query expression, it is sometimes useful to store the result of a sub expression in order to use it in subsequent clauses. You can do this by using `let` clause
```c#
var query = from  sentence in strings
		  let words = sentence.Split(' ')
		  from word in words
		  let w = word.ToLower()
		  where w[0] == 'a' || w[0] == 'e'
				|| w[0] == 'i' || w[0] == 'o'
			select word;
```
##### `ascending`
- Used in the `orderby` in query expression to specify that the sort order is from smallest to largest.
##### `descending`
- Used in the `orderby` in query expression to specify that the sort order is from largest to smallest .