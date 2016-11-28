# Javascript Cheat Sheet

## Data Types

Primitives (passed by value)
- string
- number
- boolan

Special values
- null
- undefined
- NaN
- Infinity

Non-primitive
- object literal {}
- array []
- function

## Truthy or Falsy

Truthy
- non-empty string
- true
- number not zero
- Infinity
- {} object literal, can be empty
- [] array, can be empty

Falsy
- empty string
- false
- number 0
- null
- undefined
- NaN

## References

Identifier = value
- value sets type of variable

Primitives (number, string, boolean)
- always passed by value and not by refererence

Non-primitives ([], {}, function)
- always passed by reference

'''javascript
var x = 3, y = 4;
// scope is between { } of function and it's as if variable is declared immediately after the opening {, sometimes called hoisting
// if var is declared in the global scope, think of the globale scope being enclosed with an opening { and closing }

let x = 3, y = 4;
// block scope between {}, also checks if identifier has already been used

const x = 3, y = 4;
// same as let scope but checks identifier and type
'''

**var** is replaced by **let** and **const**.

Use **const** before **let** because it prevents overwriting a variable and also does type checking.  

## Object

Objects are a collection of key and value pairs. Every object has a special pointer to another object. We call this pointer a **prototype**.  

A key is a string. If key is a "valid" string we can use the dot operator. ES6 now allows for a numeric key.
'''javascript
	const obj = Object.create(null)	// creates an empty object
	obj.key		// undefined, use this syntax for a valid key string
	obj['key']	// also undefined,  use this syntax for an invalid key string
'''
In javascript a key is generally refered to as a **property**.  To get an array of the enumerable keys or properties of an object use *Object.keys(obj)*.

Every **propery** (key) has some meta information.
- enumerable, if true shows up when we loop over an object's properties
- configurable, if true may delete it
- writable, if true can update value of property
- value, set the value associated with the property  

To read **property** meta information use *Object.getOwnPropertyDescriptor(obj, 'property')*.
'''javascript
	const obj = Object.create(null)	// create empty object
	Object.defineProperty(	obj,	// object onto which property is attached
							 'a',		// property name
							  {value:1,	// property value
							   writable:true,
							   enumerable:true,
							   configurable:true})
	o.a		// value 1
	Object.getOwnPropertyDescriptor(obj, 'a')	// read property meta information
	// {value: 1, writable: true, enumerable: true, configurable: true}
'''

**Prototype chain**

When we look for a property on an object:
- first look on the object itself
- then look on the object pointed to by the prototype pointer, and so on
- follow the prototype chain untill a *null*  value is returned

'''javascript
	let parent = Object.create(null)							// create object
	Object.defineProperty(parent, 'a', {value: 1,				// create property on object
									    writable:true,
									    enumerable:true,
									    configurable: true})

	let child = Object.create(parent)
	Object.getPrototypeOf(child)			// returns parent object
	'''

**Shared methods**

Method sharing can be done with each object in the prototype chain. Remember a property can have a function as a value. 	

	// 'person' contains the method to be shared
	let person = Object.create(null)
	Object.defineProperty(person,  'fullname',
							{value: function() => {firstname + ' ' + surname},
							writable:true,
							enumerable:true,
							configurable: true})

	let john = Object.create(person)
	john.firstname = 'John'	// local property
	john.surname = 'Smith'	// local property
	john.fullname()			// John Smith, function is found on prototype object 'person'


**Shorthand With Object Literals**

	let obj = {a:1, b:2, c:3}	// object literal shorthand

	/* Approximately equivalent to
	*	let obj = Object.create(Object.prototype);
	*	obj.a = 1
	*	obj.b = 2
	*	obj.c = 3 */

An object literal always sets the newly created object (*obj* in this case) to the object located at *Object.prototype*. This object has a number of standard methods.
- constructor
- *hasOwnProperty*('property'), true or false
- *isPrototypeOf*(object), true or false
- *propertyIsEnumerable*('property'), true or false
- toLocaleString
- *toString*(), string representation of object, often overwritten
- *valueOf*(), primitive value

	let obj = Object.prototype
	obj.constructor	// reference to a function
	obj.hasOwnProperty('hasOwnProperty')	// true
	obj.isPrototypeOf(Object)	// true
	obj.propertyIsEnumerable('toString')	//false
	obj.toString()	// "[object Object]"
	obj.valueOf()	// Object

Object literal syntax has evolved significantly with new ES6 features.
- *\_\_proto\_\_* sets prototype object
- shorthand method declaration with just *methodName*
- another shorthand method with just *methodName() { }*
- computed, dynamic property names *[ 'prop_' + (() => 42)() ]: 42*

	let obj1 = {a:1, b:function() {console.log("function b")}}
	// create obj2
	let obj2 = {	c:5,
		__proto__: obj1,		// set obj2 prototype to point to 'obj1'
		someFunc			// property name implies value also equal to someFunc
		doFn() {console.log('doFn'); console.log(this);}	// property name is 'doFn'
		// equivalent to doFn: function doFn() {console.log('doFn'); console.log(this);}
		[ Math.PI]: 42
	    }

	someFunc(a,b) {console.log(a+b)}

	obj2.b	// 'function b'
	obj2.someFunc(2,3)	// 5
	obj2.doFn()	// 'doFn', this refers to obj2
	obj2.3.141592653589793	// syntax error
	obj2[3.141592653589793]	// 42



**Masking references in local object where same reference is on the prototype chain**

	let o1 = {a:1, b:2}
	let o2 = Object.create(o1)
	o2.a = 20	// does not overwrite o1.a but masks it
	o1.a	// still 1

## Interrogate An Object

Often we want to know stuff about an object. Local properties versus properties on the prototype chain. For an object we may want to know:
- local properties and values
- object that **prototype** is pointing to
- all of this recursivelly upward untill the "root" object is reached

To get properties of an object:
- *Object.getOwnPropertyNames(obj)*, local enumerable and non-enumerable properties
- *Object.keys(obj)*, local enumerable properties
- *for (property in obj) {console.log(property)}*, local and prototype objects enumerable properties


	// create obj1 as an object literal
	let obj1 = {a:1, b:function() {console.log("function b")}}

	// create obj2 with prototype pointing to obj1
	let obj2 = Object.create(obj1)
	obj2.c = "This is "c" property value"
	// add another property that is not enumerable
	Object.defineProperty(obj2, 'd', {writable:true, configurable:true, enumerable:false, value:3})

	// get all (enumerable and non-enumerable) local properties
	Object.getOwnPropertyNames(obj2)	// array of all local properties ["c", "d"]

	// for only enumerable local properties
	Object.keys(obj2)	// ["c"]

	// log local key(enumerable true/false) value
	Object.getOwnPropertyNames(obj2).forEach( (key) => {
		console.log(key + ' (' +  Object.getOwnPropertyDescriptor(obj2, key).enumerable + ') ' + 		obj2[key]);
  		})

  	// get the object pointed to by obj2.prototype
	let p1 = Object.getPrototypeOf(obj2)
	p1 === obj1	// true

	// get the object pointed to by p1
	let p2 = Object.getPrototypeOf(p1)
	Object.getPrototypeOf(p2) === null	// shows p2 is root or top of prototype chain

## Merging objects

It is possible to create a new object that includes existing source objects. These source objects are then "merged" into a single *target* object.
- targetObject = *Object.assign(target, ...sources)*
- enumerable and local *source* properties only
- source properties are assigned (property value updated) if the property name already exists in the *target* object
- for properties with the same name in the source objects, the right most object is assigned to target
- properties that are merged into target are local to the target property. The target object holds no reference to the source objects. This can make for a "heavy" object.

	let obj1 = {d:4, e:5}	// source
	let obj2 = {c:3, d:8}	// source
	// notice how right most obj1.d takes precedence
	let result = Object.assign({}, obj2, obj1)	// target, {c:3, d:4, e:5}
	// notice how right most obj2.d takes precedence
	result = Object.assign({}, obj1, obj2)	// target, {c:3, d:8, e:5}
	result = Object.assign( {a:1, b:2, c:20, d:30}, obj2, obj1)	// {a:1, b:2, c:3, d:4, e:5}
	Object.keys(result) 	// ['a', 'b', 'c', 'd', 'e']

## Program Flow

Program flow can be altered by:
- if/else, switch, and ternary operations
- error handling
- event listening and the callback functions that run when these events occur

#### Standard Procedural Flow Control
- if/else if/else
- ternary operator, condition ? *expr1* if condition true : *expr2* if condition false, returns expression
- ternary also allows multiple, comma-seperated statements to executed
- switch statement,
- switch statement can often be replaced by an object literal {case1:value1, case2:value2 ...}

		// Standard Procedural Flow Control
		// if else
		if (condition) {
			// if true
		}
		else {
			// if false
		}

		// if/ else if/ else
		if (condition1) {
			// conditionN true
		}
		else if (condition2) {
			// conditionN true
		}
		else if (condition3) {
			// conditionN true
		}
		else {
			// all conditionN false
		}

		// ternary
		let result = condition  ?  exprTrue to return if true  :  exprFalse to return if false

		let obj1 = {a:1, b:2}
		let condition = true
		let result = condition ? (obj1.a = 5, 10) : 20
		// result = 10 and obj1.a = 5

		let condition = false
		let result = condition ? (obj1.a = 5, 10) : 20	// result = 20


		// switch statement
		switch ('TestForThis') {
			case 'NotTestForThis':
				// some statements
				break;
			case 'AlsoNotTestForThis':
				// some statements
				break;
			case 'TestForThis':
				// some statements
				break;
			default:
				// will run if no 'break'
				// will be ignored if 'break' 	
		}

		switch ('ok') {
			case 'notOk':
				break;
			case 'ok':
				console.log('ok');
				break;
		}
		ok

		switch ('ok') {
			case 'notOk':
				break;
			case 'ok':
				console.log('ok');
			default:
				console.log('default');	// no 'break' at case 'ok'
		}
		ok
		default

		switch ('ok') {
			case 'notOk':
				break;
			case 'ok':
				console.log('ok');
				break;
			default:
				console.log('default');
		}
		ok

		// replace switch statement with object literal
		obj = {'ok': 'ok"}
		let switch = 'ok'
		console.log(obj[switch])
		ok


#### Error Handling And Reporting		

Error handling may alter program flow.
- do something about the error using *try / catch / final*
- log the error and possibly report it to a central log

*try / catch / final* usage:
- generally should be avoided
- use to test json data for correctness

	try {
		// 'try' block executed as normal code path
	}
	catch {
		// 'catch' block only executed if error thrown inside 'try' block
	}
	final {
		// 'final' block always executed
	}

	// example of checking for well-formed JSON data
	function isJsonOk = function(jsonStr) {
		try {
			JSON.parse(jsonStr);
		}
		catch (error) {
			// note: don't throw an error
			return false;
		}
			returnt true;


In a browser, *windows.onerror* event usage:
- the *windows.onerror* event is fired when an error occurs
- very good way to centrally log all errors
- can also send the errors to a central server

	window.onerror = function(msg, url, lineNo, columnNo, error) {
	 	// error is an object with properties name, message, stack
	 	console.log('window.onerror function');
	  	console.log('msg: ' + msg);
		console.log('url: ' + url);
		console.log('lineNo: ' + lineNo);
		console.log('columnNo: ' + columnNo);
		console.log('error.name: ' + error.name);
		console.log('error.message:' + error.message);
		console.log('error.stack: ' + error.stack);	// shows full stact trace
		// send errors to central server here
	}


Throwing an error:
- always create an error object
- *throw new Error("error message")*
- will cause *window.onerror* event
- the Error object will include msg, lineNo, columnNo, and error.stack properties


	// how to throw an error, should generally be avoided
	if (someError) {
		throw new Error('some error message');
	}

	// a throw can be caught with window.onerror
	window.onerror = function(msg, url, lineNo, columnNo, error) {
		// 'error' is an object
	}

## Understanding the event queue and single threaded call stack

The JavaScript engine is single threaded. This means only one line of code can be executed at a time. For a js program to execute (with no events) we can literally start at the top and end at the bottom. It is very rare for js to execute like a procedural program.

When a function, *fn1*, is called it is placed on the call stack. If a *fn2* is called from inside *f1*, this is also placed on the call stack. And suppose *fn3* is called inside *fn2*, then this too is placed on the call stack.

	call stack		call stack after		call stack after		call stack after
					fn3 returns			fn2 returns			fn1 returns			
		fn3			
		-----
		fn2			fn2
		-----		-----
		fn1			fn1					fn1					<empty>
		-----		-----				----				----

When the call stack is empty the JavaScript engine can accept *events* on the event queue. When an *event* occurs that the JavaScript engine is looking for, a function associated with that *event* is executed. This function is placed on the call stack.


	let button = document.getElementsByTagName('button')[0];	// references a DOM element
	// listen for event 'button clicked' on the event queue
	button.addEventListener('click', (event) => {		// 'event' object contains a bunch of info
  		console.log('Button clicked');
  		runFn();
	});

	call stack					call stack							call stack
	nothing happening			button clicked						runFn() invoked

	<empty>						button.addEventListener				runFn
																	button.addEventListener			

It is crucial to have this event model in mind when programming in JavaScript.

Much of the time we are waiting for an event that has been placed on the event queue. If the call stack is empty the event is serviced immediately by running a function that we associate with that event. Untill that function returns, the call stack is busy and no further events can be accepted. When that function returns and the call stack is empty, the next queued event is accepted by the JavaScript engine. The idea is to run and complete these functions very quickly, so as to be ready to service the next event.

### Callbacks and Closures

Callback functions are a very important concept in JavaScript.

The callback function is that function which will execute when some event occurs in the future. The function maintains context by means of closures. Context through closures remains available to the callback function even though the function that spawned the future callback has long since disappeared.

In the below example runFn spawns the timer *setTimeout* with callback function *timeout*. The timer event is only triggered after at least 5,000 msec. By that time runFn is long since cleared from the call stack. Yet function *timeout* still has access to the values of a, b, and c.

The closure is because the curly braces { and } enclose the callback function 'timeout'. In this particular instance once runFn has completed the variable a,b,c can only be access by the *timeout* function. As such their values cannot be altered.

The global object always has a closure over any callback function. But these values can be altered before the timer event occurs. The *timeout* function reads the values at the a, b, c reference.  

	function runFn() {						// closure runFn  
  		console.log('runFn');
  		let a = 10;

  		function fn2() {						// closure fn2
    			console.log('fn2');
    			let b = 20;

    			function fn3() {					// closure fn3
			      console.log('fn3');
			      let c = 30;
			      console.log('start setTimeout');
			      // function 'timeout' is a callback
			      // this is the function that will run when the timer expires event occurs on the event queue
			      // although we have exited 'runFn' when the 'timeout' function is executed
			      // the function still has access to the values of a, b, c
			      // These references are maintained because js has the property of closures
			      setTimeout(function timeout() {
				console.log('timeout');
				console.log(a);
				console.log(b);
				console.log(c);
			      }, 5000)
    			}
    		fn3()
  		};
  	fn2()
	}

	let button = document.getElementsByTagName('button')[0];
	button.addEventListener('click', (event) => {
	  console.log('Button clicked');
	  console.log(event);
	  runFn();
	});

#### Understanding *this* inside a callback





## Explicit Loops

	for (let i=0; i<x.length; i++) {
		break;
		continue;
	}

	while (condition) {
		break;
		continue;
	}

	do {
		break;
		continue;
	} while (condition);


	for (const each of x) {
		break;
		continue;
	}


	for (const each in x {	// deprecated, do not use
		break;
		continue;
	}


## Implicit Looping Over Arrays

**el**		element in array
**index**	index of element in array
** arr**		array
**el0**	previous element in array

	//Do something with each element but don't actually return anything using *forEach*.
	arr.forEach((el[, index, arr]) => {} [, thisContext]);

	//Reduce an array to a single value using *reduce* and return a single value
	arr.reduce((el[, el0, index, arr]) => {} [, initialValue]);

	//Transform every element in an array using *map* and return an array of same size.
	arr.map((el[, index, arr]) => {} [, thisContext]);

	//Filter or reduce the elements in an array using *filter* and return an array.
	arr.filter((el[, index, arr]) => {} [, thisContext]);

	//Returns true if every element in array satisfies a particular condition
	arr.every((el[, index, arr]) => {} [, thisContext]);

	//Returns true if at least one element in array satisfies a particular condition.
	arr.some((el[, index, arr]) => {} [, thisContext]);

## Functions

When a function is called in javascript a few properties are implicitly created.
- **arguments**, supports indexes and length
- **this**, this refers to an object

Functions can also be defined using the arrow function syntax =>. Arrow function properties are:
- always anonymous
- **arguments** does not exists
- **this** object is preset and cannot be altered by call(), apply() or bind().
- implied return of item inside ( ), but can still use explicit **return** statement  

		// function reference is 'fn1', note default argument values
		function fn1(x=3, y=4) {
			arguments;
			arguments[0];
			arguments.length
			this
			return true;
		}

		// passsing multiple arguments using spread operator ...args 	
		function fn2(x=3, y=4, ...args) {
			arguments; 		// [1,2,3,4,5,6]
			args;		 	// [4,5,6]
			this
			return false;				
		}
		fn2(1,2,3,4,5,6)

		// arrow function
		let fn3 = (x=3, y=4, ...args) => { // function body}

		// arrow function, implied return placed in ()
		let fn1 = (x=1, y=2) => ({x:x}); 		// place returned object in (  )

## Pain in the butt 'this'

**this** is a javascript keyword that refers to an object. The difficulty is knowing exactly which object **this** is actually referring to. **this** is similar to **self** used in Python.

#### Rules for deciding what **'this'** points to:

##### Traditional function

**this** depends on how a traditional **function()** is called.

- Functions defined in the global scope always have **this** pointing to the global object

- Whenever a function is called by a preceding dot, the object before the dot is **this**

- Whenever a constructor function is used, **this** refers to the specific instance of the object that is created and returned by the constructor function

- Whenever JavaScript’s call or apply method is used, **this** is explicitly defined.

##### Arrow function

For an arrow function => **this** refers to what **this** is when the function is called. Suppose we have a reference to an arrow function called 'arrowFn'.

- If 'arrowFn' called when  **this** = global/ window then **this** inside the called function refers to same/ global.

- If 'arrowFn' called when **this**= 'cat' then **this** inside the called function refers to 'cat' object.




		// x.b uses traditional 'function' and 'this' references 'x' object
		var x = {a:1,  b: function() {console.log(this)} }

		// y.b used arrow 'function' and 'this' references global object
		var y = {a:1, b: () => {console.log(this)} }

		var x1 = {a:1,
				 b: () => {
				 	console.log(this);		// global object
				 	let aa = function() {
				 		console.log(this);	// global object
				 		};
				 	aa()
				 	}
			 	};

		x1.b();	 	


		var x = {a:1,
				b:  () => {
					console.log(this);		// global object
					let aa = () => {
						console.log(this);	// global object
						};
					aa()
					}
				};
		x.b();


		var y1 = {a:1,
				b: function() {
					console.log(this);							// y1 object
					let aa = function() {console.log(this);};	// global object !!!!
					aa();
					}
				}
		y1.b();

		var y = {a:1,
				 b: function() {
				 	console.log(this);						// y1 object
				 	let aa = () => {console.log(this);};	// y1 object
				 	 aa();
				 	 }
		  	      }

		y.b();




		d = {  a: 1,
			  b: function() {
			  	console.log(this);	// 'd' object
			  	let fn1 = () => {
			  		console.log(this);	//  'd' object
			  		let fn2 = () => {
			  			console.log(this); }; 	//  'd' object
			  			fn2(); };
		  		fn1();	 
	  		}
  		};
  		d.b() ;	// d.b() calls a traditional function  			


		d = {  a: 1,
			  b:  () => {
			  	console.log(this);	// global object
			  	let fn1 = () => {
			  		console.log(this);	//  global object
			  		let fn2 = () => {
			  			console.log(this); }; 	//  global object
			  			fn2(); };
		  		fn1();	 
	  		}
  		};
  		d.b() ;	// d.b() calls an arrow function  			

##### bind, call, apply

Allows **this** object to be designated when calling a traditional function (not an arrow function).

bind
- used to bind **this** in a callback
- func.bind(thisArg)
- func.bind(thisArg, arg1, arg2)
- "thisArg" becomes the **this** in the called function

call, apply
- invokes function immediately
- func.call(thisArg, arg1, arg2)
- func.apply(thisArg, [arg1, arg2])


	function func() {console.log('a running'); console.log(arguments)}
	func.call(window, 1, 2)	// arguments = [1, 2]
	func.apply(window, [1, 2])	// arguments = [1, 2]

	setTimeout(a.bind(window, 1,2,3),1000)
	// a running
	// [1,2,3]


## Useful Items

Spread operator
- ...x
- expand expression **x** into an array
- **x** can be function arguments (1, 2, 3) or an array
- make copy of an array

	// expand function arguments
	functiofn1 = (x=1, y=2) => ({x:x});n fn(...args) {args; // [10, 20, 30]}
	fn(10,12,30)

	// expand in array
	arr1 = [1, 2, 3]
	arr2 = [...arr1, 4, 5, 6]			// [1, 2, 3, 4, 5, 6]

	// copy an array
	arr1 = [1, 2, 3]
	arr2 = [...arr1]				// copy of arr1
