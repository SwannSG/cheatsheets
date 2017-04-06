## Node Express

[Express Documentation](https://expressjs.com/)

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
// establish a route to a resource
app.<http 1.1 method>(url string, function(req, res) {} )
```


### Middleware


