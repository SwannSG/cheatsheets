## Node Express

[Express Documentation](https://expressjs.com/)

### Basic HTTP

All messages have the form:
 - start line
 - headers
 - body


*request* message
```javascript
<method> <request-URL> <version>
<headers>
<entity-body>

// example request
start line-> GET /test/hi-there.txt HTTP/1.1
headers----> Accept: text/*
             Host: www.joes-hardware.com
body-------> <empty>
```

*response* message
```javascript
<version> <status> <reason-phrase>
<headers>
<entity-body

// example response
start line-> HTTP/1.0 200 OK 
headers----> Accept: text/* 
             Host: www.joes-hardware.com Headers Content-type: text/plain
             Content-length: 19
body-------> Hi! I’m a message!
```

### Minimalist Web Server

A web server understands communications across the *http* or *https* protocol.

Create a "web server" listening on port 3000.

```javascript
const express = require('express');
const app = express();

app.listen(3000, 'localhost',function expressApp(){
    console.log('app is listening on localhost:3000');
});
```

### Routing

Client communication to the web server can find a path to a *resource* on the server based on two criteria:
 - url string
 - http/1.1 method
     - CONNECT, DELETE, GET, HEAD, OPTIONS, PATCH, POST, PUT, TRACE

```javascript
// establish a route to a resource end point
app.<http 1.1 method>('url string', function(req, res) {} )

// resource end point: GET /route_1 
app.get('/route_1', (req,res){})
```

### Middleware

[Writing middleware](https://github.com/robertjd/writing-express-middleware)



