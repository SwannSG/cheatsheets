# ECMAscript 6

## Variable Declaration

##### Identifiers
- identifier = value
- don't use a keyword
- letters, numbers, underscore, dollar (_abc, $abc, abc, abc123) are fine
- cannot start with a number (~~2be~~)

##### let, const, versus var
- blocks scope for *let, var*
- once declared value of *const* cannot be changed
- *let, var* springs to life where declared

```javascript
let x = 3, y = 4; 	// block scope between {}, checks if identifier already used, variable exists where declared, variable ceases to exist outside its' {} block
const x = 3, y = 4; 	// same as 'let' scope but checks identifier and does not allow value to change
var x = 3, y = 4; // behaves like declaration is at the top of global scope or function

// useful consequence of let versus var
for (let i=1; i<=10; i++) { // using 'let'
  createMyCallback(i);  // when createMyCallback runs a snapshot of the 'i' value at time createMyCallback runs is used i = 1,2...,10  
}
for (var i=1; i<=10; i++) { // using 'var'
  createMyCallback(i);  // when createMyCallback runs the value of  'i' at the time createMyCallback runs is used i = 10  
}
```
## String Handling

#### Unicode
There is better support for Unicode

#### Substrings

##### includes, startsWith, endsWith, repeat();

```javascript
let s = 'abd-def-ghi';
s.indexOf('def')  // 4
s.includes('def') // true
s.startsWith('a') // true
s.endsWith('a') // false
'x'.repeat(3)   // 'xxx'
```
#### Regular Expressions
Some addtional support for regular expressions

#### Template Literals

Template literals are enclosed in *backticks*.
Is a string.
Can make substitutions using ${varName}. Actually, ${evaluate expression} is what really happens. So ${3+2} will work.

Multiline strings
Basic string formating
HTML escpaing

```javascript
let x = `a template literal`;   // note the backticks

// just write the multiline string, no trickery
let msg = `Multiline
string
new
to ES6`

// variable substitutions
let firstname = "John";
`${firstname} Bloggs` // John Bloggs
`${3+2} Bloggs` // 5 Bloggs
```

##### Tagged Templates

In below *myTag* is a Tagged Template function.

```javascript
// tagged literal function
// literals contains array of literal strings
// subs contains array of substitutions  
function myTag(literals, ...subs) { // notice the spread operator
  console.log(literals);  // ['abc-', '-ghi-', raw: Array[3]]
  console.log(subs);     // ['def', 'jkl']
}
let var1 = 'def'';
let var2 = 'jkl';
let x = myTag`abc-${var1}-ghi-${var2} `;
```

## Functions

#### Default parameters

```javascript
function f1(x=1, y=2) {
  return x + y;              
}
f1()  // 3
```
#### Rest parameters

Takes separate parameters and consolidates them into a single array.

```javascript
function f2(x, y, ...otherArgs) {
    return otherArgs; // [3, "a", 4, 5]
}
f2(1, 2, 3, 'a', 4, 5);
```
#### Spread parameters

Takes an array as a parameter and spreads array entries into separate parameters.

```javascript
function f3(x, y, arr) {
  return Math.max(...arr) // 6  
}
f3(8, 9, [3, 4, 5, 6]);
```
#### function name property

All functions, even anonymous ones, now get a name property. Usefull for debugging.

```javascript
function f2() {}
f2.name   // 'f2'

let f3 = function() {}  // anonymous function
f3.name   // 'f3'  
```
#### Arrow functions

**arguments** does not exist.

**this** object is preset and cannot be altered by call(), apply() or bind().

For an arrow function => **this** refers to what **this** is when the function is called. Suppose we have a reference to an arrow function called 'arrowFn'.

- If 'arrowFn' called when  **this** = global/ window then **this** inside the called function refers to same/ global.

- If 'arrowFn' called when **this**= 'cat' then **this** inside the called function refers to 'cat' object.

```javascript
let x = arg1 => 2*arg1; // fn name is 'x', Single parameter 'arg1' passed. Returns '2*arg1'.
// equivalent to
function x(arg1) {
  return 2*arg1;
}
```

```javascript
let x = (arg1, arg2) => arg1 + arg2;
// equivalent to
function x(arg1, arg2) {
  return arg1 + arg2;
}
```

```javascript
let x = () => ({a:1, b:2});
// equivalent to
function x() {
  return {a:1, b:2};
}
```
```javascript
// immediately invoked function
let x = ((x) => {
  return {
    getProp: function() {return x;}
  }
})('abc');
```
## Expanded Object Functionality

- *Ordinary objects*, default behaviour
- *Exotic objects*, different behaviour
- *Standard objects* eg. Array, Date
- *Built-in objects*, which includes all *Standard Objects*

#### Shorthand syntax and Computed Property names

```javascript
function returnObj(a, b) {
  // return {a:a, b:b};
  return {a, b};
}

function returnObj(a) {
  return {
    a,
    b() {return 2*a;}   // method shorthand, name of method is 'b'
  }
}

// computed property names
function returnObj(a,b) {
  return {
    a,
    [b]: 'b'  // computed property name
  }
}
b = returnObj(3, 'dog') // {a:3, 'dog': 'b'}
```

#### Object methods

*Object.is* to compare objects
*Object.assign* to compose a target object from a bunch of objects
```javascript
o1 = {1:1, 2:2};
o2= {1:1, 2:2};
Object.is(o1, o1)  // true
Object.is(o1, o2) // false
tgt = {}
Object.assign(tgt, o1, {3:3, 4:4
tgt   // Object {1: 1, 2: 2, 3: 3, 4: 4}  
```
Object.getOwnPropertyNames() order of property names is now well defined. Order as follows:
- numeric keys in ascending order
- string keys in the order added to object
- symbol keys in the order added to object

Object.setPrototypeOf() to change an objects prototype.

```javascript
let obj1 = {    // create obj1
  sayHello() {console.log('Obj1');}
}
let obj2 = {    // create obj2
  sayHello() {console.log('Obj2');}
}
let obj3 = Object.create(obj1);   // obj3 prototype points to obj1
Object.getPrototypeOf(obj3).sayHello()  // 'Obj1'
Object.setPrototypeOf(obj3, obj2)  // change prototype of obj3 to obj2
Object.getPrototypeOf(obj3).sayHello()  // 'Obj2'
```
Prototype access with *super*
- can only be used in a concise method
- points to a method on the object's prototype

```javascript
obj3 = {
  sayHelloOnProto() {super.sayHello();},  // run method 'sayHello' on 'obj3' prototype
  sayHello() {console.log('Obj3');}
}
Object.setPrototypeOf(obj3, obj1);
obj3.sayHello();  // 'Obj3'
obj3.sayHelloOnProto() //'Obj1'
```
## Destructuring

#### Object Destructuring - going from object to variables
- destructure an object with variable names same as property names.
- assign property values on an object to already created variables.
- assign property values on an object to already created variables, and set default values on certain variables.
- destructure an object and change the variable names so they are differet from the original property names.

```javascript
let obj = {a:1, b:2, c:3};
let {a,b,c} = obj;  // creates variable a=1, b=2, c=3

// a, b already defined
let obj1 = {a:10, b:11};
({a, b} = obj1);  // 'obj1' values assigned to a,b - notice it is an expression in brackets
// a=10, b=11, c=undefined

({a, b, c=12} = obj1); // although 'c' isn't in obj1, we can initialise it to 12
// a=10, b=11, c=12

// assignment to different local variable names
({a: new_a, b: new_b} = obj1);
```
#### Nested, targeted Destructuring
- can destructure and rename precisely what you want.
```javascript
obj = {a: {a:1,
   b:2,
   c: {a:21, b:22}  // we just want a:21, b:22 and we want to rename the variables 'my_a', 'my_b'
} ,
b:52,
c:61
}
let {a: {c}} = obj  // c.a=21, c.b=22
let {a: {c: {a: my_a, b: my_b}} } = obj;  // my_a=21, my_b=22
```
#### Array Destructuring - array to variables

```javascript
let arr = [1,2,3,4,5];
let [a,b,c,d,e] = arr;  // notice the [] brackets for arrays
// a=1, b=2, c=3, d=4, e=5

// a,b already defined
[ [a,b] = [10,11] ];  // a=10, b=11

// a,b,c already defined - initial value
[ [a=20,b,c]=[5,6,7] ]; // a not equal to 20, a=5, b=6, c=7
[ [a, b, c=20]=[15, 16] ];  // a=15, b=16, c=20

// swapping variables
// a=15, b=16 before
[a, b] = [b, a];
// a=16, b=15 after

// nested array descructuring
arr = [1, 2, [3, 4, [5, 6]], 7];
[, , [, , [j,k]]] = arr;  // j=5, k=6

// rest of the items
arr = [1,2,3,4,5];
[a, ...theRest] = arr; // theRest = [2, 3, 4, 5], a=1
```
#### Using Destructuring in function arguments

```javascript
function fn1(a, b, {c, d, e, f} = {}) {
  console.log(a, b, c, d, e, f);
}
fn1(1,2, {c:3, f:4}); // inside fn1: a=1, b=2, c=3 d=undefined, e=undefined, f=4
fn1(1,2); // 1 2 undefined undefined undefined undefined
```
## Symbols and Symbol Properties

Primitives are *strings, numbers, Booleans, null , undefined* and now *symbols*.
Symbols provide a unique reference to a property. These Symbol properties are not enumerable.

```javascript
let firstname = Symbol('firstname'); // Symbol(firstname)
let surname = Symbol('surname'); // Symbol(surnname)
let name = {
  [firstname]: 'Joe',
  [surname]: 'Bloggs'
}
// Object {Symbol(firstname): "Joe", Symbol(surname): "Bloggs"}
name[firstname] = 'Skaap';
```
#### Global Symbols

```javascript
// 'app_name' placed in global symbol registry
let gSym1 = Symbol.for('app_name');
let gSym2 = Symbol.for('app_name');
(gSym1 === gSym2) // true

// non-global Symbols are always unique
sym1 = Symbol('app_name');
sym2 = Symbol('app_name');
(sym1 === sym2) // false
```
#### Retrieving symbols from an object

```javascript
// retrieves all enumerable properties on an object
Object.keys(name); // empty array []

// retrieves all properties in an object but not Symbol properties
Object.getOwnPropertyNames(name); // empty array []

// retrieves all Symbol properties
Object.getOwnPropertySymbols() // [Symbol(firstname), Symbol(surname)]
```

#### Exposing Internal Operations with Well-Known Symbols

Allows access to certain internal operations within ECMAscript.

## Sets and Maps

#### Sets

```javascript
let set = new Set(); // Set {}
set.add(3); // Set {3}
set.delete(3) // Set {}
set = new Set([1, 2, 3, 4, 5, 5, 5, 5]); // Set {1, 2, 3, 4, 5}
// looping thro' set
set.forEach(function(value, key, ownerSet) {
    console.log(value, key, ownerSet); // value = key, ownerSet = Set {1, 2, 3, 4, 5}
})
// convert set to array
arr = [...set];
```
#### Weak Sets

```javascript
let weakSet = new WeakSet();
weakSet.add({1:1})  // can only add a {} object or [] array  to a weak set
```

#### Maps

Maps allow key and value association. (why use maps instead of an object literal).
- Used for a key-value collection.
- Key can be an object.

```javascript
let map = new Map();
map.set('key', 'value'); // Map {"key" => "value"}
map.delete('key'); // Map {}

map.set('key', 'value1');
map.get('key') // Map {"key" => "value1"}
map.clear() // delete all entries

map.set('key1', 'value1');
map.set('key2', 'value2');
map.set({a:1, b:2}, 10);
map.forEach(function(val, key) {
  console.log(val, key);
  console.log(map.has(key)); // true
  })
```
#### WeakMaps

Similar to maps but not sure why we use them.

## Generators and Iterators

#### Generator
Inside the generator function
- function defined with *function \*myGen(x)*
- yield keyword which returns an object {val: someVal, done: someBool}.
- when the yield is encountered the state of the function is perfectly maintained.

How to interact with the generator function
- the first time a generator function is initialised it does not actually run *gen = myGen()*.
- we call the generator function each time using *gen.next()*.
- when the generator is complete the object *(val: someVal, done: true)*, done is set to true

Gotchas
- cannot use arrow function for a generator function
- *yield* immediate parent function must be a generator

```javascript
function *gen() { // note asterisk in front of function name
  yield 'abc';    // new keyword called 'yield'
  yield 'def';
}
let iter = gen();
iter.next();  // Object {value: "abc", done: false}
iter.next();  // Object {value: "def", done: false}
iter.next();  // Object {value: undefined, done: true} // 'done' is set to true
```

```javascript
function* gen(arr) {
  arr.forEach((x) => {yield x;})  // throws an error, yield can only be used directly (immediate parent) inside a generator function
}
iter = gen([1,2,3,4])

function* gen(arr) {
  for (let i=0; i= arr.length; i++) {
    yield arr[i];
  }
}
iter = gen([2,3]);
iter.next();  // Object {value: 2, done: false}
iter.next();  // Object {value: 3, done: false}
iter.next();  // Object {value: undefined, done: true}

// generator function expression
let gen = function*() {}
```
##### Make a collection iterable

Suppose we have a collection of objects, and we wish to make these iterable. In this context iterable means we want the *for (let item of collection)* to work in a standard manner.

```javascript
collection = {
  items: [1, 2, 3],    // standard array
  *[Symbol.iterator]() {
      for (let item of this.items) {
        yield item; // yield returns object {value: item, done: boolean}
      }
    }
}
for (item of collection) {
  console.log(item)   // 1, 2, 3
}
```
ES6 has three standard collection types. Arrays, Maps, and Sets. These all have standard methods to "extract" data in the *for/of loop*.
- entries(), [key, value]
- values() default and returns just value
- keys() returns just the keys

```javascript
let arr = [1,2,3];
for (let each of arr) {
  each  // value 1,2,3
}

// these are identical
for (let each of arr.entries()) {
  each  // [0, 1], [1, 2], .. [key, value]
}
for (let [key, value] of arr.entries()) {
  key, value  
}
// end these are identical

for (let each of arr.keys()) {
  each  // 0,1,2
}
```
#### Standard string iterators

We can also iterate over a string using for/of construct

```javascript
s = 'abcdefghi'
for (x of s) { console.log(); } // a, b, c, ...
```

#### Advanced stuff

We can pass arguments via *iter.next(arg1)* to the next yield statement.
It is also possible to throw errors *iter.throw(new Error("Error!")))*.

```javascript
function* gen() {
  let first = yield 1;
  console.log(first);
  let second = yield first + 3;
}

iter = gen();
iter.next();  // 1
iter.next(10);  // 13
```
Generators can also delegate (call) to other generators.

```javascript
function* gen1() {
  yield 'gen1-1';
  yield 'gen1-2';
}
function* gen2() {
  yield 'gen2-1';
  yield 'gen2-2';
}
function* gen0() {  // gen0 calls other generators
  yield 1;
  yield *gen1();  // note the *
  yield 2;
  yield *gen2();
}
iter.next() //Object {value: 1, done: false}
iter.next() //Object {value: "gen1-1", done: false}
iter.next() // Object {value: "gen1-2", done: false}
iter.next() // Object {value: 2, done: false}
iter.next() // Object {value: "gen2-1", done: false}
iter.next() // Object {value: "gen2-2", done: false}
iter.next() // Object {value: undefined, done: true}
```
A task runner. Requires more work. Can be used with promises to simplify asynchronous programming.

```javascript
function* run() {
  let todo_high_level = [ 'do1', 'do2', 'do3'] // high level todo
  let todo_detail = todo.map( (x,i) => {return {todo: x, timer: 2*i + 2};}) // translate high level todo to detail view
  setTimeout(function() {}, each.timer)
}
https://github.com/nicholaswyoung/nodevember-2015/blob/master/examples/generator-promises.js

```
