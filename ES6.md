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
iter = gen0();
iter.next() //Object {value: 1, done: false}
iter.next() //Object {value: "gen1-1", done: false}
iter.next() // Object {value: "gen1-2", done: false}
iter.next() // Object {value: 2, done: false}
iter.next() // Object {value: "gen2-1", done: false}
iter.next() // Object {value: "gen2-2", done: false}
iter.next() // Object {value: undefined, done: true}
```
Using iterators with promises to simplify asynchronous programming.
https://github.com/nicholaswyoung/nodevember-2015/blob/master/examples/generator-promises.js

```javascript
const messages = [    // array of messages
  "Hello, Nodevember.",
  "It's time to talk about generators...",
  "Generators are part of the ES2015 specification,",
  "and they allow us to handle variable length sequences with ease."
];

// returns a promise
function promise(msg) {
  return new Promise(function(resolve, reject) {
    resolve(msg); // value of promise resolve
    })
}

// generator
function* gen(msgs) {
  for (val of msgs) {
    yield promise(val);
  }
}

// getting all the promises in sequence
iter = gen(messages)
while (true) {
  let result = iter.next()
  if (result.done) { break;}
  console.log(result);
}
```
## Classes

JavaScript is not a class based language. However, it can be made to mimic class type behaviour. Below is the classic construct pre-classes.
- local properties and method in the constructor are on the instance
- properties and methods are placed on the prototype using *Animal.prototype*

```javascript
// Animal class constructor
function Animal(type) {
  this.type = type;  // property that will be available on the instance
}
// add a method to Animal
// method is on the prototype not the instance
Animal.prototype.getType = function() {
  console.log('I am a ' + this.type);
}

let dog = new Animal('dog');
dog.getType() // 'I am a dog'
```

ES6 formalised classes that captures best practices.

```javascript
class PersonClass {
  // equivalent of the Animal constructor
  constructor(name) { // name is a passed argument
    // own instance properties and methods are created here, and only here, in the constructor
    this.name = name;   // property on the instance
  }
  // equivalent of PersonType.prototype.sayName
  sayName() { // method on the prototype
    console.log(this.name);
  }
}
// instantiate an instance of the class PersonClass
person =  new PersonClass('steve');
person.sayName() // 'steve'

person instanceof PersonClass // true
person instanceof Object // true
typeof PersonClass // function
```
We can also have a class expression'

```javascript
let PersonClass = class { // anonyous class
}

let PersonClass = class PersonClass2 { // named class expression
}
```
Classes are first class objects and may be passed into functions.

```javascript
// immediatle invoked class - singleton
let person = new class {
  constructor(name) {
    this.name = name;
  }
  sayName() {
    console.log(this.name);
  }
}("Nicholas");
```
*Accessor Methods* create getter and setters to access a constructor property. Use keywords *set, get*

```javascript
class someClass {
  constructor(sound) {
    this._sound = '' // treat as a private property because of underscore
    if (this._validateSound(sound)) {
      this._sound = sound;
    this._show = false;
  }

// validate any value assigned to this._sound
  _validateSound(sound) {
    if (sound === 'loud' || sound === 'soft') {
      return true;
    }
    return false;
  }
  get sound() {
    console.log('get sound');
    return this._sound;
  }
  set sound(x) {
    console.log('set sound')
    // do some error checking/ validation here
    if (this._validateSound(x)) {
      this._sound = x;  
    }
  }
}

inst = new someClass('loud');
inst.sound // 'get sound', 'loud' - uses getter
inst.sound = 'soft' // 'set sound', 'soft' - uses setter
```

Computed method names. Also can apply to getter and setter names.

```javascript
computedName = 'myMethod';
class Test {
  constructor(x) {
    this._x = x;
  }
  [computedName]() {
    console.log('Method with computed name "myMethod"')
  }
}
inst = new Test('x');
inst.myMethod() // 'Method with computed name "myMethod"'
```
Generator methods can also be used *\*generator*. To itterate over a collection class use *\*[Symbol.iterator]()* as the method name.

Static method

```javascript
class Test {
  constructor(x) {
    this._x = x;
  }

  one() {   // on the prototype of the instance
    console.log('one');
  }

  static one() { // accessed by Test.one()
    console.log(this._x); // undefined, there is no access to the instance property !
    console.log('static one');
  }
}
inst = new Test(3);
inst.one(); // 'one'
Test.one(); // 'static one'
```
#### Inheritance

Inheritance is refered to as "derived classes" in ES6.

*super()* in the constructor() of the derived class.
- brings 'this' to life
- exposes the parent properties

```javascript
class Human {
  constructor() {
    this.arms = 2;
    this.legs = 2;
  }
  armsAndLegs()  {
    return (this.arms + this.legs)*2;
  }
}

class Male extends Human {
  constructor() {
    super();  // brings 'this' to life
    this.sex = 'male'
  }

  armsAndLegs()  {
    return this.arms + this.legs;
  }

  protoArmsAndLegs() {
    return super.armsAndLegs()  // calls armsAndLegs on the prototype
  }

}
m = new Male(); // Male {arms: 2, legs: 2, sex: "male"}
m.protoArmsAndLegs() // 8
m.armsAndLegs() // 4
```
static method is also inherited and placed on the derived class.

```javascript
class Test {
  constructor() {
    this.a = 'a'
  }
  static mouse() {
    console.log('static mouse');
  }
}
class Best extends Test {
  constructor() {
    super();
  }
}
inst = new Best()
Best.mouse() // 'static mouse'
```
Derived classes from expressions

```javascript
function Rectangle(length, width) { // constructor function
  this.length = length;
  this.width = width;
}
Rectangle.prototype.getArea = function() {
  return this.length * this.width;
}
function getBase() {
  return Rectangle;
}

class Square extends getBase() {  // getBase feeds in Rectangle class
  constructor(length) {
    super(length, length) // passes length into Rectangle.length, Rectangle.height
  }
}
sq = new Square(3);
sq.getArea() // 9
sq isinstance of Rectangle // true
```
Derived classes using mixins

```javascript
let SerializableMixin = {
  serialize() {
    return JSON.stringify(this);
  }
};
let AreaMixin = {
  getArea() {
    return this.length * this.width;
  }
};
function mixin(...mixins) {
  let base = function() {};
  Object.assign(base.prototype, ...mixins);
  return base;
}
class Square extends mixin(AreaMixin, SerializableMixin) {
  constructor(length) {
    super();
    this.length = length;
    this.width = length;
  }
}

Inheritance from built-in types is now supported

```javascript
class myArray extends Array {
  skaap() {
    console.log('skaap');
  }
}
my_array = new myArray();
my_array.skaap() // 'skaap'
```
## Improved Array Capabilities

```javascript
arr = Array.of(1,2,3) // [1,2,3]

// create array from something
arr = Array.from(arguments) //
arr = Array.from(arguments, (value) => value + 1) // mapping function

// find first occurence of
arr.find()
arr.findIndex()

// fill an array with a particula value
arr.fill()

arr.copyWithin()
```

#### Typed Arrays

For speed. These arrays are numeric only. *ArrayBuffer* and *DataView*. Very specialised area for high preformance.

## Pomises and Asynchronous programming

A promise has 3 states.
- pending
- settled resolved
- settled rejected

Example of 3 states with a *get* request to a server.
- pending. We are waiting for a response from the server.
- settled resolved. We have a 200 positive response from the server.
- settled rejected. We have a 404 negative response from the server.

How is a promise created ?

A promise is a special object, created from the Promise class.

*promise = new Promise( executorFunction( xResolve, yReject ) {} )*

```javascript
promiseA = new Promise( function myExecutorFunction(resolve, reject, success=true) {
  success ? resolve('valueWeWantToSendToThen') : reject('valueWeWantToSendToCatch')  
})

promiseA
  .then( x => { console.log(x); }) // 'valueWeWantToSendToThen'
  .catch( x => {console.log(x); }) // 'valueWeWantToSendToCatch'
```
- the Promise constructor assigns the names of the arguments to special, pre-written functions. You don't have to write them.
- typical names are "resolve" and "reject".
- *resolve(valueWeWantToSendToThen)* or *reject(valueWeWantToSendToCatch)*
- the *valueWeWantToSend...* can be a primitive, array, object, or anything. But only a single argument can be passed.

What happens inside the promiseA.then( fn1(x) ) ?
- when promiseA settles, fn1(x) is executed.
- it takes a single argument, set inside promiseA by resolve(valueForNext)

What does fn1( x) return ?
- .then( x => 'someValue' )
- .then( x => Promise.resolve( 'someValue' ) )
- .then( x => { return 'someValue'; } )
- .then( x => { return Promise.resolve( 'someValue' ) } )
- the above are all equivalent
- a promise is always returned where the value for the next then is set to 'someValue'
- in all the above cases the state of the promise is "settled resolved"

```javascript
promiseA = new Promise( function myExecutorFunction(resolve, reject, success=true) {
  success ? resolve('valueWeWantToSendToThen') : reject('valueWeWantToSendToCatch')  
})

promiseA
  .then(  x   => 'then1: ' + x )
  .then(  x   => Promise.resolve('then2: ' + x) )
  .then(  x)  => { return 'then3: ' + x; }  )
  .then(  x   => { return Promise.resolve( 'then4: ' + x); }  )
```

What if we want fn1(x) to return a "settled rejected" status ?
- .then( x => Promise.reject( 'someError ') )
- .then( x => { return Promise.reject( 'someError' ) } )
- we need to explicitly show the promise is rejected by using Promise.reject

```javascript
promiseA = new Promise( function myExecutorFunction(resolve, reject, success=true) {
  success ? resolve('valueWeWantToSendToThen') : reject('valueWeWantToSendToCatch')  
})

promiseA
  .then(  x   =>  Promise.reject( 'someError ') )
  .then( x => { return Promise.reject( 'someError' ) } )
  .catch( x => console.log(x) )
```

Below is an example of asynchronous calls to a server. *fetch* returns a promise with a value set to a Response object. *rsp.ok* is a boolean propety on the Response object.

We use the ternary operator and return *rsp.json()* or Promise.reject(....).

```javascript
function seq() {
  let obj = {}  // object to populate
  fetch('https://jsonplaceholder.typicode.com/posts')
    .then( x => x.ok ? x.json() : Promise.reject('error: posts') )
    .then( x => { obj.posts = x;  return fetch('https://jsonplaceholder.typicode.com/comments');} )
    .then( x => x.ok ? x.json() :  Promise.reject('error: comments'))
    .then( x => { obj.comments = x; return obj} )
    .then( x => { window.obj = obj; })  
    .catch( err => console.log(err) )
}
seq()
obj   // Object {posts: Array[100], comments: Array[500]}
```

We can also execute multiple asychronous calls and wait for them all to complete.

```javascript
Promise.all([fetch('https://jsonplaceholder.typicode.com/posts'), fetch('https://jsonplaceholder.typicode.com/comments')])
  .then( x => x[0].ok && x[1].ok ? Promise.all([x[0].json(), x[1].json()]) : Promise.reject('error') )
  .then( x => { return {posts: x[0], comments: x[1]}; })
  .then( x => { window.result = x;})
  .catch( err => console.log(err) ); 






```
