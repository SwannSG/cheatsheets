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

In *express* the above corresponds to:
 - res.statusCode (200)
 - res.statusMessage ('OK')


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

"http://localhost:3000/route_1?abc=1&def=2" will access path "/route_1". The key-value pairs are not part of the route. In this case the route is */route_1*.

How do we extract the key-value pairs inside a query ?
 - remember a query starts with a "?" *url?key1=val1&key2=val2

```javascript
app.get('/route_1', (req, res) => {
    req.query   // {key1:val1, key2:val2}
    res.send('ROUTE: route_1', req.query);
})
```

#### Access params /:parame_name substitution in URL

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

Browser's only have two mechanisms for sending data.
 - XMLHttpRequest (XHR)
 - fetch

### XMLHttpRequest

XMLHttpRequest precedes *fetch* by many years, and reflects a programming style that has changed. It has the ability to abort a transfer from the browser to the client, something *fetch* does not have.  

[XMLHttpRequest](https://en.wikipedia.org/wiki/XMLHttpRequest)

XMLHttpRequest is a constructor.
```javascript
const xhr = XMLHttpRequest
```

 - xhr.open( Method , URL, Asynchronous, UserName, Password )
    - Method is an http 1.1 method (GET, PUT, ....)
    - URL is a string. It represents the path to a resource on the server
    - Asynchronous is a boolean. True if asynchronous (default) 
```javascript
xhr.open('POST', '/json-handler')
```

 - xhr.setRequestHeader(header, value)
    - The first parameter of this method is the text string name of the header.
    - The second parameter is the text string value.
    - This method must be invoked for each header that needs to be sent with the request.
    -  Any headers attached here will be removed the next time the open method is invoked in a W3C conforming user agent.
    - If we are sending JSON in the body we need to set "Content-Type" to "application/json;charset=UTF-8"  
```javascript
xhr.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
```

 - xhr.send(data)
    - sends data to the server
    - data can be any serializable data stream
        - JSON.stringify(object)  
```javascript
xhr.send(JSON.stringify({ email: "hello@user.com", response: { name: "Tester" } }));
```

 - xhr.onreadystatechange = function() {}
    - binds a function to *xhr* that will run asynchronously each time an event occurs.
    - internally sets a property called *readyState* that is updated each time an event occurs
        - readyState = 1, xhr.open has been run
        - readyState = 2, headers received from server but body content not yet loaded
        - readyState = 3, body content loading
        - readyState = 4, body content has finished loading
```javascript
xhr.onreadystatechange = function() {
    let readyStateDescription = {1:'open', 2: 'headers received', 3:'loading', 4:'loaded'}
    let readyStateText = readyStateDescription[this.readyState]
    if (readyStateText === 'loaded') {
        console.log('success');
    }
}
```         
 - xhr.abort()
    - prevents xhr.onreadystatechange event handler firing
    - 

### fetch

*fetch* is a modern mechanism to send (and receive) data from a browser.

[fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

fetch(URL, {Bunch of options})

The bunch of options are:
 - method: value is an http 1.1 method
 - headers: Header object
    - new Header({headerProperty: value, ... })
 - body: data can be any serializable data stream, like JSON

```javascript
result = fetch('/json-handler', {
            method: 'post',
            headers: new Headers({
                'Content-Type': 'application/json;charset=UTF-8'
            }),
            body: JSON.stringify({ email: "hello@user.com", response: { name: "Tester" } })
        });
result
    .then(x => {})
    .catch(err => {})
```

fetch returns a promise.
 - promise is resolved with promise value
 - promise is rejected with promise value


#### Sending JSON data from browser to Express 

To send data from the browser to the server, we need to use the *XMLHttpRequest()* constructor.   

```javascript
const xhr = new XMLHttpRequest();   // new HttpRequest instance 
xhr.open("POST", "/json-handler");
xhr.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
xhr.onreadystatechange = function() {
    if (this.readyState == 4) {
        if (this.status == 200) {
            // success
            console.log('success');
        }
        else {
            // failure
            console.log('error');
        }
    }
};
xhr.send(JSON.stringify({ email: "hello@user.com", response: { name: "Tester" } }));
```

Sending json using the *fetch* API
```javascript
fetch('/json-handler', {
	method: 'post',
    headers: new Headers({
		'Content-Type': 'application/json;charset=UTF-8'
	}),
	body: JSON.stringify({ email: "hello@user.com", response: { name: "Tester" } })
});
```

### Receiving JSON data from server in browser 

This really has nothing to do with **node**.

#### XMLHttpRequest

```javascript
criteria = {h_c: {
    'hcp': [[13,13],'x','x','x','x'],
    'suit': [[5,7],'x','x','x']
    },
ps_c: {
    'hcp': [[26,26],'x','x','x','x'],
    'suit': [[9,9],'x','x','x']
    }}


const xhr = new XMLHttpRequest();   // new HttpRequest instance 
xhr.open("POST", "/get_new_hand");
xhr.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
xhr.onreadystatechange = function() {
    if (this.readyState == 4) {
        if (this.status == 200) {
            // success
            console.log('success');
            let jsObject = JSON.parse(this.response); 
        }
        else {
            // failure
            console.log('error');
        }
    }
};
xhr.send(JSON.stringify(criteria));
```
*xhr* has a bunch of methods and properties as shown below. The body content is available in *xhr.response*. The content is a JSON string. To change the JSON string to a javasctipt object we use *JSON.parse(xhr.response)*. 

```javascript
UNSENT : primitive : 0
OPENED : primitive : 1
HEADERS_RECEIVED : primitive : 2
LOADING : primitive : 3
DONE : primitive : 4
onreadystatechange : function
readyState : primitive : 4
timeout : primitive : 0
withCredentials : primitive : false
upload : object
responseURL : primitive : http://localhost:8080/get_new_hand
status : primitive : 200
statusText : primitive : OK
responseType : primitive : 
response : primitive : {"meta": ....."iteration": 161}
responseText : primitive : {"meta": ....."iteration": 161}
responseXML : object
open : function
setRequestHeader : function
send : function
abort : function
getResponseHeader : function
getAllResponseHeaders : function
overrideMimeType : function
onloadstart : object
onprogress : object
onabort : object
onerror : object
onload : object
ontimeout : object
onloadend : object
addEventListener : function
removeEventListener : function
dispatchEvent : function
```

#### fetch API

When we use the *fetch* API a promise is returned whose value is the response object. The response object has a number of methods and properties available.

```javascript
// fetch(....)
//  .then(response => {return response.json();})
//response object methods and properties
type : primitive : basic
url : primitive : http://localhost:8080/get_new_hand
redirected : primitive : false
status : primitive : 200
ok : primitive : true
statusText : primitive : OK
headers : object
body : object
bodyUsed : primitive : false
clone : function
arrayBuffer : function
blob : function
json : function
text : function
```

In below note the sequence of *thens* carefully.

The first ".then", *.then(response => {return response.json();})* receive the response object as a parameter when the promise resolves.
 
The response object has a json method available which we can call. The json() method takes the body content which is a JSON string and converts it to a javascript object. Except it doesn't. Because this JSON conversion can be a long running operation, response.json() returns a promise.

When the json operation completes and the promise is resolved, the promise value is a javascript object that was generated from from the JSON in the body.  

```javascript
criteria = {h_c: {
    'hcp': [[13,13],'x','x','x','x'],
    'suit': [[5,7],'x','x','x']
    },
ps_c: {
    'hcp': [[26,26],'x','x','x','x'],
    'suit': [[9,9],'x','x','x']
    }}

fetch('/get_new_hand', {
	method: 'post',
    headers: new Headers({
		'Content-Type': 'application/json;charset=UTF-8'
	}),
	body: JSON.stringify(criteria)
})
.then(response => {return response.json();})
.then(jsObject => {do something with jsObject})
.catch(err => console.log(err))
```

### Express middleware - bodyparser

To process an incoming request to the server, where the request contains relevant *body* content, **bodyparser** middleware must be used.

By default *req.body* is undefined i.e. the body is not parsed. To parse the body we use body-parser. 

[Body-parser](https://github.com/expressjs/body-parser#bodyparserjsonoptions)

#### To parse incoming json data use bodyparser.json()

```javascript
const bodyParser = require('body-parser');
app.post('/json-handler', bodyParser.json(), (req,res) => {
    req.body; // { email: "hello@user.com", response: { name: "Tester" } }
    res.sendStatus(200);
})
```

### Parsing form data sent to server

The form tag has a number of attributes that describe what a server can expect when the form is submitted.
 - action-----path to a resource, url
 - method-----http 1.1 verb
 - enctype----encoding type for the form
     - default---------------"application/x-www-form-urlencoded"
     - "multipart/form-data"---used for binary data transfer
 - the form data is sent inside the body content
 - browsers represent this content as a FormData object

#### Form Data encoded as "application/x-www-form-urlencoded"

```javascript
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title of the document</title>
</head>
<body>
    <form action="/form" method="post" enctype="application/x-www-form-urlencoded">
        First name:<br>
        <input type="text" name="firstname" value="Mickey">
        <br>
        Last name:<br>
        <input type="text" name="lastname" value="Mouse">
        <br><br>
        <input type="submit" value="Submit">
    </form> 
</body>
</html>

app.get('/form', (req,res) => {
    console.log('GET /form');
    res.sendFile('/home/swannsg/development/nodeLearn/expressPlay/form.html');
});

app.post('/form', bodyParser.urlencoded(), (req,res) => {
    console.log('POST /form');
    console.log(req.body) // { firstname: 'Mickey', lastname: 'Mouse' }
});
```

For URL encoded form data, the body content follows the rules for URL syntax query 

```javascript
// Form Data
firstname=Mickey&lastname=Mouse

// parsed Form Data
firstname:Mickey
lastname:Mouse
```

#### Form Data encodes as multipart/form-data

Now the data is sent to the server differently. *bodyparser* cannot deal with this encoding and we need an alternative middleware tool.

```javascript
// Headers
Content-Type:multipart/form-data; boundary=----WebKitFormBoundaryYUTtrAWaB4w41IBt

// Body Content
Request Payload

------WebKitFormBoundaryIcqUBQUCAW8ols3d
Content-Disposition: form-data; name="firstname"

Mickey
------WebKitFormBoundaryIcqUBQUCAW8ols3d
Content-Disposition: form-data; name="lastname"

Mouse
------WebKitFormBoundaryIcqUBQUCAW8ols3d--
```







### [Express Error Handling](https://expressjs.com/en/guide/error-handling.html)


It is possible to limit the size of data that may be uploaded to the server. There are other options settings as well, *bodyParser.json(OPTIONS)*.

```javascript
const bodyParser = require('body-parser');
// limit to input body content of 1 byte
app.post('/json-handler', bodyParser.json({limit:1}), (req,res) => {
    req.body; // { email: "hello@user.com", response: { name: "Tester" } }
    res.sendStatus(200);
})
```

When we violate the upload size *bodyParser.json({limit:1})* in the above, *bodyparser* will automatically generate an error and send it to the client. But in this scenario when *bodyparser* runs it issues a res.end() and the user function never gets to run. Additionaly we may wish to log the error centrally, so we need an alternative mechansim to get greater control.

If we wish to catch the error and handle it ourselves in a more controlled manner, we need to have an error catcher function. The function signature is *errorCatcher(error, req, res, next)*. 

The error handling function, *errorCatcher*, should run as the last middleware function.

```javascript
function fn1(req, res, next) {
    next()
}

function fn2(req, res, next) {
    next();
}

app.post('/json-handler', bodyParser.json({limit:10000}), fn1, fn2,  errorCatcher,  (req,res) => {...}
```


```javascript
// global error handler
const events = require('events');
const errorEmitter = new events.EventEmitter();
// listener
errorEmitter.on('error', function(x) {  
    console.log("errorEmitter.on('error'.. listener has run")
    console.log(x);
})
// end global error handler

function errorCatcher(error, req, res, next) {
    console.log('errorCatcher');
    if (error) {
        // set response and send directly to client
        res.statusCode = error.statusCode;
        res.statusMessage = error.message + ' own custom error message';
        res.send();
        // add some global error handling
        errorEmitter.emit('error', error)
        res.end();
    }
    next();
}

app.post('/json-handler', bodyParser.json({limit:10000}), fn1, fn2,  errorCatcher,  (req,res) => {
    console.log('/json-handler');
    console.log(req.body);
    res.statusCode = 200; // is actually automatically set to 200
    res.send(JSON.stringify({ a: 1, b:2 }));
})
```








