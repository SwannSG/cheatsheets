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
