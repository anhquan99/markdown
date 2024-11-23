# `JSON.stringify(object)`
- Convert object to string
- Ignore function properties, symbolic, properties that store `undefined`
 ``````js
let student = {
	name: 'John',
	age: 30,
	isAdmin: false,
	courses: ['html', 'css', 'js'],
	wife: null
};

let json = JSON.stringify(student);
alert(typeof json); // we've got a string!
alert(json);

/* JSON-encoded object:
{
 "name": "John",
 "age": 30,
 "isAdmin": false,
 "courses": ["html", "css", "js"],
 "wife": null
}
*/
  ``````
- **There must be no circular references**