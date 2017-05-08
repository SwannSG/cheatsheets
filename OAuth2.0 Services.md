## OAuth 2.0 Services

[Overview](https://www.youtube.com/watch?v=io_r-0e3Qcw&t=283s&ab_channel=KnpUniversity)

[Oauth2 Model Specification](https://github.com/oauthjs/node-oauth2-server/wiki/Model-specification)

[Oauth2 documents](https://oauth2-server.readthedocs.io/en/latest/index.html)

### Password grant authorisation

This is a self-contained solution. No third party is involved.

When a user registers with the node app, a *user* document is created in *users*.
 
```javascript
// users
user = {
    username: // String, unique
    password: // String, hash is stored
    scope: // Array of strings, added by node app not user
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

### Oauth2 model for password grant

### Authorisation code flow for Google

[Code Flow](https://developers.google.com/actions/develop/identity/oauth2-code-flow#handle_user_sign-in)
[API Manager](https://console.developers.google.com/apis/credentials/oauthclient/212202313428-6mc89ovul11felrj2obproaoda1gpr5u.apps.googleusercontent.com?project=rock-prism-144813)
[Google scopes](https://developers.google.com/identity/protocols/googlescopes)
[Google Oauth Playground](https://developers.google.com/oauthplayground/?code=4/3O6DUCNBTcHd9WgkPdTgQIZm976G8Iqdhr7UTTPDwUQ#)







