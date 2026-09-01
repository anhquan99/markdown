# Map reduce pattern
- Handle big data problems.
- A `MapReduce` program is composed of 2 tasks: **map** and **reduce**
- To implement this pattern, we need to start by writing a `map` function that takes in data (key/value pair) as a single input value and converts it into another set of intermediate data (key/value pair). The user then writes a `reduce` function that takes the output from the map function (key/value pair) as input and combines the data with a smaller dataset containing any number of rows of data.
# The fork/join pattern
- In fork/join patterns, work is forked (split) into a set of tasks that can be executed asynchronously. Later, the forked work is joined in the same order or a different order, as per the requirements and scope of parallelization.
- Implementations:
	- `Parallel.For`
	- `Parallel.ForEach`
	- `Parallel.Invoke`
	- `System.Threading.CountdownEvent`
# The speculative processing pattern