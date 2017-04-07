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

A web server understands communications using the *http* or *https* protocol.

Create a "web server" listening on port 3000.

```javascript
const express = require('express');
const app = express();

app.listen(3000, 'localhost',function expressApp(){
    console.log('app is listening on localhost:3000');
});
```

### Basic Routing to a Resource

Client communication with the web server can find a path to a *resource* on the server based on two criteria:
 - url string
 - an http/1.1 method
     - CONNECT, DELETE, GET, HEAD, OPTIONS, PATCH, POST, PUT, TRACE
 - optional query key-value pairs 

```javascript
// establish a route to a res, mw_1 resource end point
app.<http 1.1 method>('url string', func_1, func_2, ...., function(req, res) {} )

// resource end point: GET /route_1 
app.get('/route_1', (req,res){})

// func_1, func_2, ... are optional and run as middleware

// the last function is also optional function(req, res) {}
// as middleware can reply directly to the client 
```

### Middleware Basics

[Writing middleware](https://github.com/robertjd/writing-express-middleware)

Middleware are:
 - functions that modify request and response objects
 - called "filters" in other languages
 - order of the filters does matter
 - function signature (req,res,next)
    - *next* is required to allow each function in the filter chain to run
 - if middleware decideds to respond directly to the client we must show end of response by using *res.end()* 

We can associate middleware with a specific route to a resource.

```javascript
function mw_1(req,res,next) {
    console.log(new Date().toJSON(), 'mw_1', req.method, req.url);    
    next();
}

function mw_2(req,res,next) {
    console.log('mw_2', req.method, req.url);    
    next();
}

// we associate middleware mw_2, mw_1 with /route_1 in that order
app.get('/route_1', mw_2, mw_1, (req, res) => {
    res.send('ROUTE: route_1');
})
```

We can associate middleware with all routes to all resources.
 - *app.use(middlewareFunction)*
 - order of *app.use(middlewareFunction)* matters

```javascript
function common_mw_1(req,res,next) {
    console.log(new Date().toJSON(), 'common_mw_1', req.method, req.url);    
    next();
}

// order matters
app.use(common_mw_1)
app.use(common_mw_2)

app.get('/route_1', mw_1, (req, res) => {
    res.send('ROUTE: route_1');
})
```

Middleware can change what get sent to the client.
 - mw_4 will respond if 'flag' set
 - else '/route_3' final function responds 

```javascript
function mw_4(req,res,next) {
    let flag = false;
    if (flag) {
        res.send('mw_4 has decided to send a response');
        res.end();
    }
    else {
        next()
    }
}

app.get('/route_3', mw_4, (req, res)=> {
    res.send('ROUTE: /route_3, normal response');
})
```

### URL Syntax

A URL is made up of these parts:
 - scheme       htttp, https
 - user
 - password
 - host         DNS hostname, ip address
 - path         string path to resource
 - query        roll your own additional parameters, not well defined
    - key-value pairs   seperated by '&'or ';'
 - frag         fragment to access part of a resource, client-side only

```javascript
<scheme>://<user>:<password>@<host>:<port>/<path>?<query>#<frag>

http://localhost:3000/route_1?abc=1&def=hello
```

To maintain transparency through a network the URL is encoded.
[HTTP URL Encoding](https://www.tutorialspoint.com/http/pdf/http_url_encoding.pdf)

#### Access query key-value pairs

"http://localhost:3000/route_1?abc=1&def=2" will access path "/route_1". The key-value pairs are not part of the route.

How do we extract the key-value pairs inside a query ?
 - remember a query starts with a "?" *url?key1=val1&key2=val2

```javascript
app.get('/route_1', (req, res) => {
    req.query   // {key1:val1, key2:val2}
    res.send('ROUTE: route_1', res.);
})
```

#### Access /:key_name in URL

Suppose we have a URL with the form *http://localhost/route_1/name/john*. And another URL with the form *http://localhost/route_1/surname/smith*. Suppose we wish to isolate name or surname.

By using a colon in the path descriptor we can capture parameter values. The parameter key is the *:key_1*.

Route matching takes place on the full pattern.

```javascript 
// http://localhost/route_1/name/john
app.get('/route_1/:name_type/:name', (req, res){
    req.params      // {name_type: 'name', name: 'john'}
})

// http://localhost/route_1/surname/smith
app.get('/route_1/:name_type/:name', (req, res){
    req.params      // {name_type: 'surname', name: 'smith'}
})
```

### Getting Request Headers

HTTP request headers can be obtained as follows:

```javascript
req.headers     // {headerKey: value, ...}
```

### Dealing with data sent to Express from the client browser

We know how to deal with "data" inside the URL. But what about when we send data inside the mesage body. Let's consider:
 - json data
 - form data
 - file upload   

#### Sending JSON data from browser to Express 

To send data from the browser to the server, we need to use the *XMLHttpRequest()* constructor.   

```javascript
const xhr = new XMLHttpRequest();   // new HttpRequest instance 
xhr.open("POST", "/json-handler");
xhr.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
xhr.send(JSON.stringify({ email: "hello@user.com", response: { name: "Tester" } }));
```

By default *req.body* is undefined i.e. the body is not parsed. To parse the body we use body-parser. 

```javascript
const bodyParser = require('body-parser');
app.post('/json-handler', bodyParser.json(), (req,res) => {
    req.body; // { email: "hello@user.com", response: { name: "Tester" } }
})
```

ExpressJS provides a simple API for doing just that. We won't cover the details of the
API. Instead, we will provide links to the detailed documentation on ExpressJS guides.
The methods in the API are self-explanatory in most cases. A sampling of the requestrelated
API is below:
req.body: get the request body.
req.query: get the query fragment of the URL.
req.originalUrl
req.host: reads the Host header field.
req.accepts: reads the acceptable MIME-types on the client side.
req.get OR req.header: read any header field passed as argument.
On the way out to the client, ExpressJS provides the following response API:
res.status: set an explicit status code.
res.set: set a specific response header.
res.send: send HTML, JSON or an octet-stream.
res.sendFile: transfer a file to the client.
07/04/2017 HTTP: The Protocol Every Web Developer Must Know - Part 1
https://code.tutsplus.com/tutorials/http-the-protocol-every-web-developer-must-know-part-1--net-31177 15/27
res.sendFile: transfer a file to the client.
res.render: render an express view template.
res.redirect: redirect to a different route. Express automatically adds the default
redirection code of 302

