### Node and Mongoose

Mongoose makes working with mongo db easier. MongoDB interacts with *documents* inside *collections*. Mongoose encapsulates this in a *Schema*. The schema relates directly to the database. However, our user application does not interact directly with the Schema. It interfaces with a *Model*. And the Model is generated from the Schema.

[Methods on objects](https://docs.google.com/spreadsheets/d/15xXP1Ry78mnX0K3JhVV_Q8q1eGGXCNFmzBAPAUTH1EU/edit#gid=0)

mongoDB ---> Schema ---> Model ---> user application

#### Connecting to mongo db

First we need to connect to the database. If *conn* is not yet "ready", mongoose inteligently queues database activities.

```javascript
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

let conn = null;            // connection object ~ db
mongoose.Promise = Promise; // use standard ES6 promises

mongoose.connect(uri.uri)   // ask for the connection
    .then( ok => conn = mongoose.connection)
    .catch( err => console.log('err', err))
```

#### Create a Schema

The schema definition consists of properties (also called keys in mongoose speak) which map to the databse.

Below we have *keys user, password, email*.

We can also put constraints on the values associated with keys. These constraints are pulled through into the model automatically.

By convention schema names start with lowercase, and model names with uppercase.

```javascript
// let userSchema = new Schema({schema definition}, {schema options})
let usersSchema = new Schema(
    {user: {type:String, index:true, unique:true, trim:true, required:true},
    password: {type:String, default:'1234567'},
    email: {type:String, required:true, match: /.+\@.+\..+/, index:true}},
    {collection:'users'}
);
```

Above defines *usersSchema* document "blueprint", with properties 'user', 'password', and 'email'. Schema options specifies collection name as "users".


#### Create a Model from the Schema

We create a model using the *mongoose.model(modelName, schema)* constructor. 

```javascript
// model User with name = 'user'
let User = mongoose.model('User', usersSchema)
```

#### Construct *document instance* from the model

A *document instance* is an object that can be saved or retrieved from the database. It is created from a model, and passed key/value pairs representing collection fields.

```javascript
// create document instance
// new someModel({document fields})
let document_instance = new User(
    {user:'Alex',password:'passwd', email:'Alex@tutorialtous.com'}
);
```

#### Save *document instance*

We persist a document by using the *save()* method.

When we save() the document is validated. 

```javascript
document_instance.save()
    .then(result => console.log('save'))
    .catch(err => console.log('save ERROR', err));
```

#### Updating a document

The document is persisted to MongoDB, and we need to change the document.

Option 1 (two db trans, doc instance)

- retrieve document instance by _id,
- change document instance,
- save document instance.
- inefficient because of two db operations.
- validation on save takes place

```javascript
// Option 1
User.findById('58c6bd497faa994850db4cbd').exec()
    // result is a document instance
    .then(result => {result.user = 'Pamela'; result.save();})
    .catch(err => console.log('ERROR', err));
```

Option 2 (single db trans, no doc instance)

- find and update document in one query/ update
- single db operation
- does NOT return document instance
- NO VALIDATION takes place

```javascript
// Option 2
User.update({_id: '58c6bd497faa994850db4cbd'}, { $set: { user: 'Peter' }}).exec()
    // result is NOT a document instance
    .then(result => console.log(result))
    .catch(err => console.log('ERROR', err));
```

Option 2a:

- as per option 2
- allows for validation with some caveats
- validation occurs on $set, $unset, $push, $addToSet
- validation does NOT occur on $inc

```javascript
// Option 2a
User.update({_id: '58c6bd497faa994850db4cbd'}, 
            {$set: { user: 'Peter' }},
            {runValidators: true, context: 'query'}).exec()
    .then(result => console.log(result))
    .catch(err => console.log('ERROR', err));
```

Option 3 (single db trans, doc instance) 
- find and update document in one query/ update
- single db operation
- does return document instance
- NO VALIDATION takes place

```javascript
// Option 3
User..findByIdAndUpdate({_id: '58c6bd497faa994850db4cbd'}, { $set: { user: 'Peter' }}).exec()
    // result is a document instance
    .then(result => console.log(result))
    .catch(err => console.log('ERROR', err));
```

Option 3a (single db trans, doc instance) 
- as per Option 3
- allows for validation as per option 2b

```javascript
// Option 3a
User.findByIdAndUpdate({_id: "58c6bd497faa994850db4cbd"},
                       { $set: { user: 'Herbert' }},
                       {runValidators: true, context: 'query'}).exec()
    .then(result => console.log(result))
    .catch(err => console.log('ERROR', err))
```

#### Finding a document(s)

A find is done on the Model.

Doing something with the document like remove or update, is really secondary to the find. First we need to find the document(s).

The _id in the result of a find is a string. Different to native Mongo where it is an ObjectId.

The *{querySelection}* object is a standard [MongoDB query](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Adding the *.exec()* after the find is to force promises.

*Model.find({querySelection})* returns a *Query object*.

To find one or more documents. Returns an array of *document instances*. 
- find
- where
- findById
- findByIdAndRemove
- findByIdAndUpdate

```javascript
let query = User.find({querySelection}) // query object

query.exec()
    .then(result => console.log(result))
    catch(err => console.log('ERROR', err))
```

To find a single document, and return a document instance (not an array of document instances)
- findOne
- findOneAndRemove
- findOneAndUpdate

It is possible to chain a *query object*. The result is an array of document instances. If we suppress the _id it is not possible to save the document.

```javascript
let query = User.find({selection})
query
    .limit(10)      // <= 10 documents
    .sort({ user: -1 }) // sort array in reverse user order
    .select({ email: 1, user: 1, _id:0 })   // properties: 1 are returned
    .exec()
    .then(result => {result.user=result.save(); console.log(result);})
    .catch(err => console.log('ERROR', err))
```    

#### Process query result using streams

Where a query returns a large array of document instances, it is more efficient to process the documents using a node *streams* interface. We use a *cursor* object.

See [Cursor Streams](http://thecodebarbarian.com/cursors-in-mongoose-45).

The *cursor* object has an extremelu useful method *eachAsync* to stream each document. 

```javascript
let query = User.find({selection})  // query object
let cursor = query.cursor()         // cursor object
cursor.eachAsync((doc) => {
        // do something to each document instance
        console.log(doc)
    })
    .then(() => console.log('done'))
    .catch(err => console.log(err)) // not sure if 'catch' works 
```

### More on Mongoose Schemas

Each key (or field, or path) in the Schema can have additional meta information associated with it.
- name of the field
- object which describes the field

```javascript
schema = new Schema{
    fieldName: {meta information}
}
```

Mongoose also has the term *path*. This is the final key that references the meta information. In the above, the *path* is 'fieldName'.

#### Meta Information Associated with a Key (fieldname)

General
- type ..........String, Number, Date, Buffer, Boolean, Mixed, ObjectId, Array
- required ......boolean, the field must be included
- default .......default value
- select ........columns to return on a query
- validate ......validateFunction(field_value), function must return boolean
- get ...........custom getter function
- set ...........custom setter function

MongoDB indexes
- index .........boolean
- unique ........boolean
- sparse ........boolean

String type specific
- lowercase ...boolean
- uppercase ...boolean
- trim ........boolean
- match .......RegExp
- enum ........[array of acceptable values]
- minlength ...minimum number of characters allowed
- maxlength ...maximum number of characters allowed

Number type specific
- min .........number, >=
- max .........number. <=

Date type specific
- min .........Date, >=
- max .........Date, <=

#### Standard Validation

Keep in mind if a *path* is *required=true*, then the field must be included. Path value validation then happens.

If *path* *required=false*, and we have path value validation, then completely leaving out the field pass validaation.

```javascript
let stuffsSchema = new mongoose.Schema(
    {
        my_string: {type:String,
                    minlength: [2, 'my_string must be at least 2 characters.'],
                    maxlength: [20, 'Username must be less than 20 characters.']}, 
        email:     {type:String,
                    match: [/^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/,
                            'invalid email']},
        colour:    {type: String,
                    enum: ['red', 'green', 'blue']},
                    message: 'my enum custom message'
        name:       {type: String, trim: true},
        option:     {type: String,
                    validate: [(value) => {if (value=='test') {return true;} return false;},
                     'option error is not correct']},
        car:       {type: String, required: [true, 'car is absolutely required']},
        count:     {type: Number, min:5, max:10},
        date:      {type: Date, min: new Date('2017-01-01'), max: new Date('2017-01-30')}  
     },
    {collection: 'stuffs'}
);

let StuffModel = mongoose.model('Stuff', stuffsSchema);
```

Any off-the=shelf validation can be expressed as:
- validation property name eg. match
- value or [value, 'custom error message] 

Custom error message is not available for *enum* validator property.

```javascript
// inside the schema .....
// required: [true, 'car is absolutely required']
// property: [boolean, 'custom message']
car:       {type: String, required: [true, 'car is absolutely required']}
```

When we save a document instance validation kicks in.

```javascript
stuff = new stuffs.StuffModel(
    {
        my_string: 'tango',
        email: 'abc@google.com',
        colour: 'pink',
        name: '  James   ',
        option: 'test',
        count: '1',
        date: new Date('2017-02-25')
    }
)

// validation occurs on doc.save()
stuff.save(function error(err){
    if (err) {
        console.log('save error', err);
    }
})
```

If we want to validate a document without attempting to write to the database, we can use *doc_instance.validateSync()*. This returns a Validation Error Object. It only validates synchronous validators.

```javascript
// validateSynch only validate synchronous fields
// validate all synchronous fields
let validation_err = doc_instance.validateSync()

// validate only the fields in the array
let validation_err = doc_instance.validateSync(['field1', 'field2'])
```

### Validation on update

An update is not normally validated !

If validation is required, we need to include some options to show that.

*{runValidators: true, context: 'query'}*

Update validation does not honour *required: true*.

```javascript
stuffs.StuffModel.findByIdAndUpdate("58cc11afed3a504383e6e17e",
   {my_string: 'tango',
    email: 'abc@google.com',
    colour: 'pink',
    name: '  James   ',
    option: 'error.test',
    count: '1',
    date: new Date('2017-02-25')},
    // forces update validation
    {runValidators: true, context: 'query'}
 ).exec()
 .then(() => console.log('update good'))
 .catch(err => console.log(err))
```






### Discriminators

[Discriminators](http://thecodebarbarian.com/2015/07/24/guide-to-mongoose-discriminators)




### Middleware


*Validation* uses *Middleware* so we need to cover the concept before moving on to validation.

Middleware is specified at the Schema level.




### Validation

1. Validation is defined in the SchemaType
2. Validation is *Middleware*

schema.pre(init|save|validate|remove)


schema.post


doc.validateSynch([array of paths])
ignores async validation 



```javascript
// validate on multiple fields
var a = new Schema({
  startDate: Date,
  endDate: Date
});

ASchema.pre('validate', function(next) {
    if (this.startDate > this.endDate) {
        next(Error('End Date must be greater than Start Date'));
    } else {
        next();
    }
});

```





### Mongoose Error Handling


#### Mongoose error messages

*mongoose.Error.messages* property shows default messages.

#### Validation error object

```javascript
stuff.save(function error(err){
    if (err) {
        console.log('save error');
        console.log(err);
    }
})
```

Validation error **err** object has 4 properties
- ValidationError .......very long technical description
- errors ................references *errors* object
- message ..............."<ModelName> validation failed"
- name .................."ValidationError"

*errors* object has N properties. N is the number of fields with validation errors. 
- fieldname ............references a detailed fieldname error object
- fieldname
- fieldname


*fieldname error object* has 8 fields
- ValidationError ......very long technical description
- message ..............message we define in Schema
- name ................."ValidationError"
- properties ...........[object]
- kind .................some short category
- path .................fieldname
- value ................fieldname value
- reason ...............undefined

We need the "fieldname" from the schema to access the *fieldname error object*.

*err.errors.<fieldname>* references the *fieldname error object*



