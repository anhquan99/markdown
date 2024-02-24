# Definition
Array is an indexed collection of data elements of same type
	- Indexed means that the array elements are numbered
	- All elements in the array must be same type becase the array is stored in consecutive memory cells. Every cell must be same type therefore same size.
# Declaring in `C#`
- Normal
	 - `data_type[] name;`
 - Array  Literal
	 - `dat_type[] name = new data_type[]{value1, value 2, ...};`
 - Fewer elements than the size specifies:
	 - `data_type[5] name = {value1, value2};`
	 - Can not have more elements than the size specifies
 - Multidimensional:
	 - `data_type[,] array2D = new data_type[,] {{value1, value2},{value3, value4}};`
# Using array
- Access by index
	- `array[index]`
	- Can not access index lower than 0 and bigger than size specified
- Multidimensional:
	- `array[indexY, indexX]`
# Copying array
- using normal assign operator will not copy array
	- ~~array_1 = array_2~~
- copy element by element
-  In .NET 3.5 and higher Array can be copied by using `Array.Copy()`
# [[Jagged array]]  vs multidimensional array
Jagged array | multidimensional arry
------------ | ------------
Array of arrays | matrix
Must initialized separately each array | Can be initialized single time

# Resize array
- In .NET 3.5 and higher  array can be resized by using `Array.Resize()`
# Function
- size()
- capacity()
- is_empty()
- at(inex)
- push(item)
- insert(index,item)
- prepend(item)
- pop()
- delete(index)
- find(item)
- resize(new_capacity)
	- When you reach capcity, double the size
	- When popping an item, if size is 1/4 of capacity, resize to half
# Time complexcity
- O(1) add/remove at the end, index or update
- O(n) add/remove at anywhere
# Space complexcity
- Contiguous in memory, high performace
- space need = (array capacity, which is >= n) * size of item, but even if 2n, still O(n) 