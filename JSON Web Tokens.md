## JSON Web Tokens

A JSON Web Token (JWT) is a JSON object that is defined in RFC 7519 as a safe way to represent a set of information between two parties. The token is composed of a header, a payload, and a signature.

header.payload.signature

![JWT Flow](https://cdn-images-1.medium.com/max/880/1*SSXUQJ1dWjiUrDoKaaiGLA.png)

### Constructing JWT

**header**
```javascript
{
    "typ": "JWT",       // type
    "alg": "HS256"      // encoding algorithim
}
```

**payload**
```javascript
{
    "userId": "b08f86af-35da-48f2-8fab-cef3904660bd" // claim
}
```

We *claim* userId is "b08f86af-35da-48f2-8fab-cef3904660bd".

A *claim* is like a key-value pair, only it can't be trusted (yet).

There are several standard *claims* [JWT Standard Claims](https://en.wikipedia.org/wiki/JSON_Web_Token#Standard_fields)
 - iss, issuer
 - sub, subject
 - exp, expiry date

**signature**
```javascript
// signature algorithm
data = base64urlEncode( header ) + “.” + base64urlEncode( payload )
signature = Hash( data, secret );
```

And we get something like this

```javascript
// header
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9    // encode clear text
// payload
eyJ1c2VySWQiOiJiMDhmODZhZi0zNWRhLTQ4ZjItOGZhYi1jZWYzOTA0NjYwYmQifQ  // encoded clear text
// signature
-xN_h82PHVTCMA9vdoHrcZxH-x5mb11y1537t3rGzcM

// JWT Token
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VySWQiOiJiMDhmODZhZi0zNWRhLTQ4ZjItOGZhYi1jZWYzOTA0NjYwYmQifQ.-xN_h82PHVTCMA9vdoHrcZxH-x5mb11y1537t3rGzcM
```

### Properties of JWT

JWT does not hide or obscure data in any way.

Verification of the attached signature shows the token was created by a bona fide source.

### Verifiying the JWT

The receiving server computes the signature based on header and payload. If the signature sent and signature computed match, the token is considered to be valid. 

### node JWT Implementation

Asynch methods
 - create signature (jws.createSign)
 - verify signature (jws.createVerify)

```javascript
const jws = require('jws');

// create signature async
jws.createSign({
    header: {alg: 'HS256'},
    payload: 'abcdef ghijk',
    key: 'secretKey'
} ).on('done', function(jwtStream) {
    console.log('jws.createSign');
    console.log('jwtStream', jwtStream);
    let header =  jwtStream.split('.',1)[0];
    header = atob(header);
    console.log(JSON.parse(header));
    // verify signature async
    jws.createVerify({
        algorithm: JSON.parse(atob(jwtStream.split('.',1)[0])).alg,  
        key: 'secretKey',
        signature: jwtStream,
    }, header).on('done', (verified, result) => {
        console.log('jws.createVerify', 'verified', verified);
        console.log('jws.createVerify', 'result', result);
    })
})

// Result
jws.createSign
jwtStream eyJhbGciOiJIUzI1NiJ9.YWJjZGVmIGdoaWpr.EwXPWgWwUd3bkJHO29A6XjMaauaec9fKpNyhNIqHVHQ
{ alg: 'HS256' }
jws.createVerify verified true
jws.createVerify result { header: { alg: 'HS256' },
  payload: 'abcdef ghijk',
  signature: 'EwXPWgWwUd3bkJHO29A6XjMaauaec9fKpNyhNIqHVHQ' }
// end Result
```













