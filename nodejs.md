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

### Getting started

From inside the directory where we want to setup the application.

*npm init*

To update local dependencies in package.json use the --save option 
*npm install express --save*
*npm install mongodb --save*
*npm install nodemon --save*

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

#### Upgrade npm package manager

For global install -g flag need to use "sudo".

```javascript
// upgrades npm to latest version
sudo npm install npm@latest -g
```

#### Namespace using *require* and *module.export*

The basic rules for the string in require(*string*) to access a module are:
    - don't include filename extension ".js" in the string. test.js becomes "test"  
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

To clear the node shell.

```javascript
console.log('\033[2J');
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

[node-mongodb-native/2.2/api/](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html)

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


### Node Error Handling

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

[Sentry Cloud Error Store](https://sentry.io/welcome/)


## npm

*npm update*

[]Sentry node js](https://sentry.io/private-xs/admin/getting-started/node/)

 
### How to support https 

For test only purposes we can generate a self-signed certificate file, and a self-signed key file.

[Generate self-signed certificates](https://www.youtube.com/watch?v=s76l4BhY3FY) 

Because these are self-signed certificates they are not considered to be secure. In Chrome browser enable the *chrome://flags/#allow-insecure-localhost*.

```javascript
const https = require('https');

var httpsOptions = {
  cert: fs.readFileSync('/home/swannsg/development/ssl/apache-selfsigned.crt'), // certificate
  key:  fs.readFileSync('/home/swannsg/development/ssl/apache-selfsigned.key') // key
}; 

https.createServer(httpsOptions, app).listen(serverPort, function () {
...........
}
```

### Automatically restarting node during development

While we are developing we are making changes all the time. For a change to take effect we need to restart node each time.

```javascript
nodemon index.js
```

[nodemon](https://github.com/remy/nodemon) automates the process.

### Dealing with the file system

 - 'path'
    - dirname (folder)
    - basename (filename)
    - extname (filename extension)
    - format (properly formatted path for os)
    - join  (combine path segments)
    - normalize (resolves . and ..)
    parse (component paths)  
 - 'fs'

Read a file
 - asych (default)
    - open
    - read
    - readFile (read entire file)
 - synch
    - readSync
    - readFileSync
Read a file chunk by chunk (usefull for very large files or only a portion of file is required)
 - open (mode) returns file descriptor
 - read (file descriptor, data length, starting position) populates buffer
    - default starting position is current file pointer
 - close 

Read an entire file (mostly used, simpler)
 - specify file encoding
 - no explicit 'open'

Writing to a file chunk by chunk
 - open
 - write

Writing an entire file
 - writeFile (write entire String or Buffer, encoding)
 - appendFile

### Streams
 - Readable
 - Writable
 - String || Buffer || Object (mode)
 - data event
 - end event
 - pipe
 - instances of EventEmitter

```javascript
// http req is a readable stream
if (req.method==='POST') {
    const reqBodyBuffers = [];  // an array of buffers
    // when we have data, place the chunk in a Buffer, apped Buffer to array
    req.on('data', chunk => reqBodyBuffers.push(Buffer.from(chunk)));
    
    // data transfer is ended, concat Buffers and convert to UTF8 string
    req.on('end', () => {
        console.log(Buffer.concat(reqBodyBuffers).toString());
        resolve();
    })
```

Streams require the "stream" module. Instance of EventEmitter.

Node has many standard streams:
 - http req
 - http res
 - process.stdin
 - process.stdout
 - process.stderr
 - fs streams
 - zlib streams
 - crypto streams
 - TCP sockets

There are developers of streams and consumers of streams. We will focus on consuming streams.

There are four fundamental stream types within Node.js:
 - Readable - streams from which data can be read (for example fs.createReadStream()).
 - Writable - streams to which data can be written (for example fs.createWriteStream()).
 - Duplex - streams that are both Readable and Writable (for example net.Socket).
 - Transform - Duplex streams that can modify or transform the data as it is written and read (for example zlib.createDeflate()).

Readable stream
 - grab a streamInstance of the stream 
 - *streamInstance.on('data', (chunk) = {})* gets a chunk of data
 - *streamInstance.on('end', () = {})* shows end of data 

Writable stream pattern
 - grab a writable streamInstance
 - *streamInstance.write('someData')*
 - *streamInstance.end('done writing data')*
 






### Buffers

[Node Buffers](https://nodejs.org/api/buffer.html)

Some underlying concepts.

ES6 allows TypedArrays.
 - actual Buffer
 - view of the buffer

```javascript
buffer = new ArrayBuffer(5);
view = new Int8Array(buffer);
```

Octet stream character representation
 - decimal (0-255)
 - hex (0xNN)

Node *Buffers* are global.

Creating *Buffers*
 - UTF-8 is default

```javascript
// Creates a zero-filled Buffer of length 10.
const buf1 = Buffer.alloc(10);
// Creates a Buffer of length 10, filled with 0x1.
const buf2 = Buffer.alloc(10, 1);
// unsafe but fast
const buf3 = Buffer.allocUnsafe(10);
// Creates a Buffer containing array
const buf4 = Buffer.from([1, 2, 3]);
// Creates a Buffer from string
const buf5 = Buffer.from('tést');
// Creates a Buffer from string using Latin-1 encoding
const buf6 = Buffer.from('tést', 'latin1');
```

The are a lot of methods associated with a buffer instance.
Buffers have an index key similar to an array. 









