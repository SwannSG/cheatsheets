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
    - don't include filename extenstion ".js" in the string. test.js becomes "test"  
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

