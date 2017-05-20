## OAuth 2.0 Services

[Overview](https://www.youtube.com/watch?v=io_r-0e3Qcw&t=283s&ab_channel=KnpUniversity)

[Oauth2 Model Specification](https://github.com/oauthjs/node-oauth2-server/wiki/Model-specification)

[Oauth2 documents](https://oauth2-server.readthedocs.io/en/latest/index.html)

[Oauth2 Full Document PDF](https://media.readthedocs.org/pdf/oauth2-server/latest/oauth2-server.pdf)

### Password grant authorisation

This is a self-contained solution. No third party is involved.

When a user registers with the node app, a *user* record is created in *users*.
 
```javascript
// users
user = {
    username: // String, unique
    password: // String, hash is stored
    scope: // Array of strings, added by node app not the user
}
```

We then register a *client* app, by adding a *client* into *clients*. This is done once by the system admin. Adding this does not involve the user.

```javascript
// clients
client = {
    clientId: 'bridgeApp',
    clientSecret: 'noSecret', // required, but not used
    accessTokenLifetime: 3600, // in seconds
    redirectUris: [],   // not used for password grant
    grants: ['password'] // specified grant-type = 'password'
}
```

The Client app will send a request to the Auth Server
 - request header
    - Authorisation: Basic <client_id>:client_secret
        - base64, btoa('bridgeApp:noSecret')
        - client_secret will be 'noSecret' 
 - request body
    - grant_type: 'password'
    - username: 'admin'
    - password: 'admin'

We also need to store the tokens node oauth2 generates. Each *token* is stored in *tokens*. These tokens are automatically generated.

```javascript
token = {
    accessToken: '97642ed4530dcf6fe2dbeb7a267e48cf2e5b21cf',
    accessTokenExpiresAt: 2017-05-04T02:18:19.420Z,
    refreshToken: 'f072d7c9b7e83153c31fb6dabaa96b6c92b88e09',
    refreshTokenExpiresAt: 2017-05-17T16:18:19.420Z,
    scope: []
    user: {}    // user info
    client: {}  // client info
```

The *accessToken* is sent to the client (unique per client per token issued).

For the client to "access a resource" in the node app, they need an accessToken string and a *scope* that allows access. 

Assuming the client has a *token*, any message (get, post, etc.) must have a header property called *Authorization: Bearer accessToken string*.

We authenticate the message, and either reject or accept the token. If we accept the token we allow "access to the resource".

```javascript
const OAuthServer = require("express-oauth-server");
let oauthModel = {
    // all functions
    getAccessToken: getAccessToken,    
    getClient: getClient,
    getUser: getUser,
    saveToken: saveToken,
    validateScope: validateScope,
    verifyScope: verifyScope,
}
const oauth = new OAuthServer({ model: oauthModel });

app.get('/access_resource', oauth.authenticate({scope:['user']}), (req, res, next) => {
    // this function is run if token and scope are valid
    console.log('/access_resource');
    console.log('res.locals.oauth.token', res.locals.oauth.token);
    res.send('/access_resource is accessible');
})
```

The middleware *oauth.authenticate({scope:['user']})* authenticates the accessToken. And checks token.scope=[..., 'user', ...] to include 'user'. If these conditions are met the next function is executed. Otherwise the request is rejected with an appropriate response.

When the next function runs, we get access to the token via *res.locals.oauth.token*. From that we can identify the user. So this also provides session management. 

### oAuth2 API Calls After Client Requests A Token - Password Grant Flow 

getClient(clientId, clientSecret)
 - clientSecret is ignored
getUser(username, password)
validateScope(user, client, scope)
 - scope not used
generateAccessToken(client, user, scope)
 - 
saveToken(token, client, user)


getClient bridgeApp
getUser admin admin
validateScope
{ _id: 590c1e0e5d171f27b4d1fba1,
  username: 'admin',
  scope: [ 'admin', 'user' ] }
{ _id: 590a157029c22d2f4c7e504f,
  clientId: 'bridgeApp',
  clientSecret: 'noSecret',
  accessTokenLifetime: 1800,
  redirectUris: [],
  grants: [ 'password' ],
  scope: [ 'user' ] }
generateAccessToken()
saveToken
token { accessToken: 'token_string',
  accessTokenExpiresAt: 2017-05-17T07:50:30.235Z,
  refreshToken: 'a2a9038da33cd520290c2c2afc38a63307e0601c',
  refreshTokenExpiresAt: 2017-05-31T07:20:30.235Z,
  scope: true }
client { _id: 590a157029c22d2f4c7e504f,
  clientId: 'bridgeApp',
  clientSecret: 'noSecret',
  accessTokenLifetime: 1800,
  redirectUris: [],
  grants: [ 'password' ],
  scope: [ 'user' ] }
user { _id: 590c1e0e5d171f27b4d1fba1,
  username: 'admin',
  scope: [ 'user' ] }
end saveToken














### Authorisation code flow for Google

[Code Flow](https://developers.google.com/actions/develop/identity/oauth2-code-flow#handle_user_sign-in)
[API Manager](https://console.developers.google.com/apis/credentials/oauthclient/212202313428-6mc89ovul11felrj2obproaoda1gpr5u.apps.googleusercontent.com?project=rock-prism-144813)
[Google scopes](https://developers.google.com/identity/protocols/googlescopes)
[Google Oauth Playground](https://developers.google.com/oauthplayground/?code=4/3O6DUCNBTcHd9WgkPdTgQIZm976G8Iqdhr7UTTPDwUQ#)


### JSON Web Token (JWT)

The modern method for tokens is JWT.

We need to generate our own JWT compliant tokens.

The advantage of this approach is that we don't have to access a database to authenticate a token, when we wish to access a resource. 

If the signature is verified, the payload can be used without further examination.

### JWT for Password Grant




### Authorisation Code Flow for Facebook

[Facebook Flow](https://developers.facebook.com/docs/facebook-login/manually-build-a-login-flow)
[Facebook Developers Portal](https://developers.facebook.com/)
[Facebook App Settings](https://developers.facebook.com/apps/249686882175779/fb-login/settings/)

sudo ngrep -d any -Wbyline -q host localhost and port 3000
