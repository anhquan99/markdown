# Definition
- Jagged array is an array of arrays
- Each of the elements is a single-dimensional array have their own block of memory
- Each element have to initialized separately
# Declare
- Normal `data_type[][] jaggedArray = new data_type[][];`
- With arrays `data_type[][] jaggedArray = new data_type[][]{ new data_type[] {value1, value2}};`
# Access
- `jaggedArray[indexY][indexX];`