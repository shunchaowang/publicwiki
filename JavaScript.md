## Enhanced Object Literal in ES6
### Create objects on the fly
```
const myObject {
    prop1: 'hello',
    prop2: 'world',
    output: function() {
        console.log(this.prop1 + ' ' + this.prop2);
    },
};

myObject.output(); // hello world
```
### Initialize objects from variables
```
// ES5
var a = 1, b = 2, c = 3;
var obj = {a: a, b: b, c: c}; // obj.a = 1, obj.b = 2, obj.c = 3

// ES6, no need for nasty repetition!
const a = 1, b = 2, c = 3;
const obj = {a, b, c}; // accomplish the same result
```
The object initialization above is useful to return objects using a *revealing module pattern*.
```
const lib = (() => {
    function sum(a, b) {return a + b;}
    function multiple(a, b) {return a * b;}
    
    return {sum, multiple};
}());
console.log(lib.sum(2, 3)); //5
console.log(lib.multiple(2, 3)); // 6
```
This is very similar to ES6 modules.
```
//lib.js
function sum(a, b) {return a + b;}
function multiple(a, b) {return a * b;}

export {sum, multiple};
```
## Object method definition shorthand
ES5 requires the *function* statement when defining a method, it's not necessary in ES6.
```
const lib = {
    sum(a, b) {return a + b;}
    multiple(a, b) {return a * b;}
};

// arrow functions version
const lib = {
    sum: (a, b) => a + b;
    multiple: (a, b) => a * b;
};
```
## Dynamic property keys
In ES5 it's impossible to use a variable as a key name, although it can be added after the object has been created.
```
var key1 = 'one',
obj = {
    two: 2,
    three: 3
};
obj[key1] = 1; 
// obj.one = 1, obj.two = 2, obj.three = 3
```
In ES6 object keys can be dynamically assigned by placing an expression in `[]` square brackets.
```
const key1 = 'one',
obj = {
    [key1]: 1,
    two: 2,
    three: 3
};

// obj.one = 1, obj.two = 2, obj.three = 3
```
ES6 also permits to using expression as the key names, and method names.
```
const i = 1,
obj = {
    ['i' + i]: i,
    ['add' + i]: x => x + i,
};

// obj.i1 = 1, obj.add1(5) = 6
```
## Destructuring, aka variables from object properties
When extracting a property value from an object into another variable, it has to be explicitly declared in ES5.
```
var myObject = {one: 'a', two: 'b'};
var one = myObject.one, two = myObject.two; // one = 'a', two = 'b'
```
ES6 supports destructuring, we can create a variable with the same name as an equivalent object property.
```
const myObject = {one: 'a', two: 'b'};
const {one, two} = myObject; // one = 'a', two = 'b'
```
It's also possible to assign properties to variables with any name using the 
notation `{propertyName: propertyVariable}`.
```
const myObject = {one: 'a', two: 'b'};
const {one: first, two: second} = myObject; // first = 'a', second = 'b'
```
## Default function parameters
In ES6 it's easier to pass a single object to a function than use a long list of arguments.
```
noteTaken {
    title: 'Object Literals',
    writer: {
        name: 'Shunchao Wang',
        occupation: 'Software Engineer',
    },
};

// ES5 with default value
function printNote(param) {
    param = param || {}; // if param is null, use an empty object as the default value
    var noteTitle = param.title || 'No title',
    noteWriterName = (param.writer && param.writer.name) || 'No writer';
    return noteTitle + ', ' + noteWriterName;
}

// ES6 with dfault value
function printNote(param = {}) {
    
}
```
## Parsing returned objects
Functions can only return one value, but it could be an object with hundreds properties and/or methods. In ES5 it's
necessary to obtain the returned objects and extract values accourdingly.
```
// ES5
var obj = getObject(),
one = obj.one,
two = obj.two;
```
ES6 destructuring makes this process simplier, and there is no need to retain the object as variable.
```
// ES6
const {one, two} = getObject();

// ES6 using readFile and writeFile methods from fs (file system)
const {readFile, writeFile} = require('fs');
readFile('file.txt'), (err, data) => {console.log(err || data);});
writeFile('file.txt', 'new content', err => {console.log(err || 'file written');});
```
## ES2018 (ES9) Rest/Spread properties
In ES2015 (ES6) rest parameter and spread operator `...` notation applied to arrays only. ES9 enables spread
functionality to objects. 
```
const myObject = {a: 1, b: 2, c:3};
const {a, ...x} = myObject; // a = 1, x = {b: 2, c: 3}
// past values to function
function restParam({a, ...x}) {}
restParam({a: 1, b: 2, c:3}) {}; // x = {b:2, c:3}
```

