# Jest (JavaScript Testing framework)
- Designed for React
- Run fast because jest runs in parallel 
- Run test file `jest` command and the file with `.test.js`, `.spec.js` the same with TypeScript
- *Testing hierarchy:* test plan ⇾ test suit ⇾ test case
## Test cases
- Create a test case with `test()` or `it()` function
### Jest matchers
- **Equality:** used for checking equality or inequality mostly for arithmetic operations
- **Truthiness:** for nullm falsy and truthy. **It is important to note that anything that's not logically true is falsy**
- **Number Matchers:** used for general arithmetic operations
- **String Matchers:** matched against a regular expression
- Jest compare the properties of the object, and it does not care about the order of the properties
## What is test suite?
- A test suit is a collection of tetst cases grouped together for execution purposes
- The goal of a test suite is to organize tests by common behavior or functionality
- If all tests within a suite pass, we can assume that the tested behavior or functionality meets expectations
- To create test suit in Jest, it uses `describe()` function
## Test asynchronous code 
- Use `resolves` for asynchronous testing without `await` or `promise`
- Use `reject` for handling error
## Mock
- The `mock()` is used to mock imported function without caring the implementation of the function, it requires function in function to return the result.
- The `fn()` is used to mock implementation of the function `mockImplementation()`
- All the dependency must be mock when testing.
- `spyOn()` 