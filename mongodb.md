

After insertOne we get a Command Result. 
What was inserted including the _id held in an array called commandResult.ops. 
```javascript
let commandResult = yield db.collection('tokens').insertOne(token);
return commandResult.ops[0];-->
```

To completely replace a document. Even if token contains a token._id it is ignored.
```javascript
yield db.collection('tokens').update({_id:existing_token._id}, token)
``` 
