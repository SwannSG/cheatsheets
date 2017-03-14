## Node JS Cheatsheet

### Node JS Philosophy

- small core
- small modules
- small surface area
- simplicity and pragmatism

### Reactor Pattern

- i/o request (Event, handler) ---> Event Demultiplexer, instantly returns control back to the application
- Event Demultiplexer does its' thing, when completed pushes onto Event Queue (data, Event, handler)
- Event Loop itterates over Event Queue
- Handler associated with an Event is executed.
- Handler may make further i/o request
- Handler then gives control back to Event Loop

### Basic Node Configuration

```javascript
app/
   app/package.json     // contains meta information about the application
   app/server.json         // the actual userland node application
```

#### package.json

[Basics of package.json](https://nodesource.com/blog/the-basics-of-package-json-in-node-js-and-npm)

*package.json* file location
```javascript
app/
   app/package.json     // contains meta information about the application
   app/server.json         
```

Example *package.json*.
```javascript
{
    "name": "node-rest-auth",
    "main": "server.js",
    "description": "A test app for authorisation and https",
    "repository":
    "dependencies": {
        "bcrypt": "^1.0.2",
        "body-parser": "~1.17.0",
        "express": "~4.15.0",
        "jwt-simple": "^0.5.1",
        "mongoose": "~4.8.5",
        "morgan": "~1.8.1",
        "passport": "^0.3.2",
        "passport-jwt": "^2.2.1"
    }
}
```

From the *app* directory, the *npm install* command will *read package.json* and install the necessary packages at the required release level.

After we run *npm install* the directory "node_modules" contains all the installed node modules.

```javascript
app/
   app/package.json     // contains meta information about the application
   app/server.json         
   node_modules/
```

#### Namespace using *require* and *module.export*

The basic rules for the string in require(*string*) to access a module are:
    - don't include filename extention ".js" in the string. test.js becomes "test"  
    - any module in *node_modules* is referenced directly. /mongodb is referenced as "mongodb"
    - "./" and "../" starting point is where the source js file is located in the directory tree
    - "./" moves downwards
    - "../" move upwards. So "../../" moves up the directory tree twice. 


```javascript
Directory structure 1
/*
directory structure 1***************************
app/
    server.js               # inside server.js    
    node_modules
                /mongodb.js # require('mongodb);
    test.js                 # require('./test');
    db/
        example.js          # inside example.js
end directory structure 1************************
*/

// inside server.js (source js file locate in "app/")
const mongo = require('mongodb');
const test = require('./test');
const example = require('./db/example);

// inside example.js (source js file locate in "app/db/")
const mongo = require('mongodb');
const test = require('../test.js');
```


Directory structure 2
```javascript
/*
directory structure 1***************************
app/
    server.js               # inside server.js    
    node_modules
                /mongodb.js # require('mongodb);
    test.js                 # require('./test');
    db/
       mydir/
            example.js          # inside example.js
end directory structure 1************************
*/

// inside server.js (source js file locate in "app/")
const example = require('./db/mydir/example')

// inside example.js (source js file locate in "app/db/mydir/")
const test = require('../../test.js');
```

To make objects and methods available, we need to export these. We do this by adding:

```javascrip
//some_script.js

someObj = 3;

function someFn() {}

module.exports = {
    someObj: someObj,
    someFn: someFn
}
```
### Node Debugging

To run script, and then get control back to the node shell, do as follows:

```javascript
// enter node shell, REPL Read-Eval-Print-Loop 
node
// from inside node shell
.load ./script.js

// script will execute and shell is available
```

### MongoDB

- *node_modues/mongodb*: The MongoDB driver for Node.js
- *node_modues/mongodb-core*: Low-level MongoDB driver library; available for framework developers (application developers should avoid using it directly)

```javascript
// inside package.json
"dependencies": {
    "mongodb": "^2.2.5",
}
```

The MongoDB Node.js Driver provides a JavaScript API which implements the network protocol required to read and write from a local or remote MongoDB database. 

#### MongoDB basic setup

```javascript
app/
    node_modules/
                mongodb/
                mongodb-core/
```

We first need to start the database.
- first setup a directory structure as below
- issue bash command to start the database 

```javascript
/*
directory structure
~/development/app/db
                    /config
                           mongo_config.txt
                    /logs
                    /storage
end directory structure
*/

/* 
mongo_config.txt file contents
systemLog:
   destination: file
   path: "~/development/app/db/logs/log.log"
   logAppend: true
   component:
      query:
         verbosity: 5
      write:
         verbosity: 5
      network:
         verbosity: 5
storage:
   journal:
      enabled: true
# processManagement:
#   fork: true
net:
   bindIp: 127.0.0.1
   port: 27017
setParameter:
   enableLocalhostAuthBypass: false
storage:
   dbPath: "~/development/app/db/storage"
end mongo_config.txt file contents
*/

/*
from bash terminal

mongod --config ~/development/app/db/config/mongo_config.txt

end from bash terminal
*/
```

With the database running, we can open another bash window and get a direct interface to Mongo from the command line. We use the *mongo* command. 

#### Node and mongo

We have exampe code showing access to a mongo database.

A typical database interaction has these steps:
- open a connection to the database, and get a *db* reference
- get a "collection" reference
- issue a mongo db command, like *collection.find({})* and get the *results*
- close the connection
- return the *result* 

All of this is done asynchronously
- open connection
- then wait, after n msec we get *db* reference
- ask for collection reference(db)
- then wait, after n msec we get *collection* reference
- issue database transaction
- then wait, after n msec we get a *result*
- close connection
- then wait
- return the *result*   

We have implemented two techniques for database access.
- standard promises approach
- combination of generators and promises

### Node and Mongoose

Mongoose makes working with mongo db easier. MongoDB interacts with *documents* inside *collections*. Mongoose encapsulates this in a *Schema*. The schema relates directly to the database. However, our user application does not interact directly with the Schema. It interfaces with a *Model*. And the Model is generated from the Schema.

mongoDB ---> Schema ---> Model ---> user application

#### Connecting to mongo db

First we need to connect to the database. If *conn* is not yet "ready", mongoose inteligently queues database activities.

```javascript
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

let conn = null;            // connection object ~ db
mongoose.Promise = Promise; // use standard ES6 promises

mongoose.connect(uri.uri)   // ask for the connection
    .then( ok => conn = mongoose.connection)
    .catch( err => console.log('err', err))
```

#### Create a Schema

The schema definition consists of properties (also called keys in mongoose speak) which map to the databse.

Below we have *keys user, password, email*.

We can also put constraints on the values associated with keys. These constraints are pulled through into the model automatically.

By convention schema names start with lowercase, and model names with uppercase.

```javascript
// let userSchema = new Schema({schema definition}, {schema options})
let usersSchema = new Schema(
    {user: {type:String, index:true, unique:true, trim:true, required:true},
    password: {type:String, default:'1234567'},
    email: {type:String, required:true, match: /.+\@.+\..+/, index:true}},
    {collection:'users'}
);
```

Above defines *usersSchema* document "blueprint", with properties 'user', 'password', and 'email'. Schema options specifies collection name as "users".


#### Create a Model from the Schema

We create a model using the *mongoose.model(modelName, schema)* constructor. 

```javascript
// model User with name = 'user'
let User = mongoose.model('User', usersSchema)
```

#### Construct *document instance* from the model

A *document instance* is an object that can be saved or retrieved from the database. It is created from a model, and passed key/value pairs representing collection fields.

```javascript
// create document instance
// new someModel({document fields})
let document_instance = new User(
    {user:'Alex',password:'passwd', email:'Alex@tutorialtous.com'}
);
```

#### Save *document instance*

We persist a document by using the *save()* method.

When we save() the document is validated. 

```javascript
document_instance.save()
    .then(result => console.log('save'))
    .catch(err => console.log('save ERROR', err));
```

#### Updating a document

The document is persisted to MongoDB, and we need to change the document.

Option 1 (two db trans, doc instance)

- retrieve document instance by _id,
- change document instance,
- save document instance.
- inefficient because of two db operations.
- validation on save takes place

```javascript
// Option 1
User.findById('58c6bd497faa994850db4cbd').exec()
    // result is a document instance
    .then(result => {result.user = 'Pamela'; result.save();})
    .catch(err => console.log('ERROR', err));
```

Option 2 (single db trans, no doc instance)

- find and update document in one query/ update
- single db operation
- does NOT return document instance
- NO VALIDATION takes place

```javascript
// Option 2
User.update({_id: '58c6bd497faa994850db4cbd'}, { $set: { user: 'Peter' }}).exec()
    // result is NOT a document instance
    .then(result => console.log(result))
    .catch(err => console.log('ERROR', err));
```

Option 2a:

- as per option 2
- allows for validation with some caveats
- validation occurs on $set, $unset, $push, $addToSet
- validation does NOT occur on $inc

```javascript
// Option 2a
User.update({_id: '58c6bd497faa994850db4cbd'}, 
            {$set: { user: 'Peter' }},
            {runValidators: true, context: 'query'}).exec()
    .then(result => console.log(result))
    .catch(err => console.log('ERROR', err));
```

Option 3 (single db trans, doc instance) 
- find and update document in one query/ update
- single db operation
- does return document instance
- NO VALIDATION takes place

```javascript
// Option 3
User..findByIdAndUpdate({_id: '58c6bd497faa994850db4cbd'}, { $set: { user: 'Peter' }}).exec()
    // result is a document instance
    .then(result => console.log(result))
    .catch(err => console.log('ERROR', err));
```

Option 3a (single db trans, doc instance) 
- as per Option 3
- allows for validation as per option 2b

```javascript
// Option 3a
User.findByIdAndUpdate({_id: "58c6bd497faa994850db4cbd"},
                       { $set: { user: 'Herbert' }},
                       {runValidators: true, context: 'query'}).exec()
    .then(result => console.log(result))
    .catch(err => console.log('ERROR', err))
```

#### Finding a document(s)

Doing something with the document like remove or update, is really secondary to the find.

To find one or more documents. Returns an array of document instances. 
- find
- where
- findById
- findByIdAndRemove
- findByIdAndUpdate

To find a single document
- findOne
- findOneAndRemove
- findOneAndUpdate









### Error Handling

Central error handling can be dealt with as follows:
- create an errorEmitter object
- tell the errorEmitter to listen for error event with label 'error'
- then handle the event in one place, not strewn all over the code


```javascript
// app/errors/error_handling.js
const events = require('events');
const errorEmitter = new events.EventEmitter();

// listener
errorEmitter.on('error', function(x) {  
    console.log(x);
})

module.exports = {
    errorEmitter: errorEmitter
}
```

```javascript
// emit the error event, listener will "hear" 
error.errorEmitter.emit('error', err)
```

