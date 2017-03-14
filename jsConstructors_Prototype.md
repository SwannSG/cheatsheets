## Javascript Prototypal OOP Model

### "Root" object

Function.prototype.isPrototypeOf(Object)    // true
Object.prototype.isPrototypeOf(Function)    // true

### Prototype-based OOP - the real way

No matter the syntactic sugar, all objects are created like this.

We create *baseObject* with **no** properties whatsoever.

```javascript
let baseObject = Object.create(null);

typeof baseObject       // 'object'
baseObject.prototype    //  undefined
```

Now add some properties. A method is a property whose value is a function.

*baseObject* now has exactly 3 properties.

```javascript
baseObject.name = 'Base';
baseObject.gender = 'male';
baseObject.description = function() {return this.name + ' ' + this.gender;}
```

We can create another object from baseObject.

```javascript
let aObj = Object.create(baseObject);

Object.getPrototypeOf(aObj) // baseObject
aObj.__proto__              // baseObject, deprecated 
```

We can add a property to *aObj*.

```javascript
aObj.happy = function() {return 'I am happy';}
```

We can use *aObj* as a prototype and create a few more aObjs'.

```javascript
let aObj1 = Object.create(aObj);
aObj1.name = 'Harold';
Object.getPrototypeOf(aObj1)        // aObj

let aObj2 = Object.create(aObj);
aObj2.name = 'Godfrey';
aObj2.description();                // 'Godfrey male'
```

Now the data structure looks like this. 

```javascript

baseObject <------------------ aObj <----------------- aObj1
           prototype=baseObject        prototype=aObj

Object.getPrototypeOf(aObj1)        // aObj
Object.getPrototypeOf(aObj)         // baseObject
Object.getPrototypeOf(baseObject)   // null
```

#### Delegation

```javascript
let base = Object.create(null);
base.fullname = function() {return this.firstname + ' ' + this.surname;}

let derived_1 = Object.create(base);
derived_1.join = function() {return this.firstname + '#' + this.surname + '#' + this.age;}

let derived_1_1 = Object.create(derived_1,
    {firstname:{value:'Steve'}, surname:{value:'Swann'}, age:{value:58}});
);
```






### new and Constructor - the pseudo way

The *new* keyword and constructor function, were used to make js code look more class-like.

When *nugget* is created, we use the *constructor function* called Animal.

```javascript
// construtor function
function Animal(name) {
    this.name = name;
    this.gender = 'female';
    this.sayName = function() {return 'My name is' + this.name;};
}

let nugget = new Animal('nugget');

typeof nugget                   // object
nugget instanceof Animal        // true
Object.getPrototypeOf(nugget)  // Object {constructor: function} 
Object.getPrototypeOf(nugget).constructor.name  // 'Animal'
```

We inherit from Animal to a a new BigAnimal blueprint as follows:

```javascript
function BigAnimal(name) {
    Animal.call(this, name);
    this.size = 'Big';
}
// set BigAnimal's prototype to an object created from Animal constructor function
BigAnimal.prototype = Object.create(Animal.prototype);
// use the BigAnimal constructor function
BigAnimal.prototype.constructor = BigAnimal;
``` 

#### Getting an object's prototype

Two ways:
- Object.getPrototypeOf(object)
- object.__proto__  // deprecated







