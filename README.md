# api documentation for  [mongoose-auto-increment (v5.0.1)](https://github.com/codetunnel/mongoose-auto-increment#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-mongoose-auto-increment.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-mongoose-auto-increment) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-mongoose-auto-increment.svg)](https://travis-ci.org/npmdoc/node-npmdoc-mongoose-auto-increment)
#### This plugin allows you to auto-increment any field on any mongoose schema that you wish.

[![NPM](https://nodei.co/npm/mongoose-auto-increment.png?downloads=true)](https://www.npmjs.com/package/mongoose-auto-increment)

[![apidoc](https://npmdoc.github.io/node-npmdoc-mongoose-auto-increment/build/screenCapture.buildNpmdoc.browser.%2Fhome%2Ftravis%2Fbuild%2Fnpmdoc%2Fnode-npmdoc-mongoose-auto-increment%2Ftmp%2Fbuild%2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-mongoose-auto-increment/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-mongoose-auto-increment/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-mongoose-auto-increment/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Alex Ford",
        "email": "alex.ford@codetunnel.com",
        "url": "Chevex"
    },
    "bugs": {
        "url": "https://github.com/codetunnel/mongoose-auto-increment/issues"
    },
    "contributors": [
        {
            "name": "Nassor Paulino da Silva",
            "email": "nassor@gmail.com",
            "url": "rossan"
        },
        {
            "name": "Misha Koryak",
            "email": "mkoryak@gmail.com",
            "url": "mkoryak"
        },
        {
            "name": "Christopher Hiller",
            "url": "boneskull"
        },
        {
            "name": "tomaskavka",
            "url": "https://github.com/tomaskavka"
        }
    ],
    "dependencies": {
        "extend": "^3.0.0"
    },
    "description": "This plugin allows you to auto-increment any field on any mongoose schema that you wish.",
    "devDependencies": {
        "async": "*",
        "chai": "*",
        "mocha": "*",
        "mongoose": "^4.1.12"
    },
    "directories": {},
    "dist": {
        "shasum": "827e051d9cc371dabe8bff1a704431d341cb79df",
        "tarball": "https://registry.npmjs.org/mongoose-auto-increment/-/mongoose-auto-increment-5.0.1.tgz"
    },
    "gitHead": "50bd2a642c7565cf0a9f2c368d437b83160ad0a6",
    "homepage": "https://github.com/codetunnel/mongoose-auto-increment#readme",
    "keywords": [
        "mongoose",
        "plugin",
        "auto-increment",
        "auto",
        "increment",
        "automatic",
        "autoincrement",
        "auto_increment",
        "autoinc",
        "auto-inc",
        "auto_inc",
        "pureautoinc",
        "mongoose-pureautoinc"
    ],
    "maintainers": [
        {
            "name": "chevex",
            "email": "Alex.Ford@CodeTunnel.com"
        }
    ],
    "name": "mongoose-auto-increment",
    "optionalDependencies": {},
    "peerDependencies": {
        "mongoose": "^4.1.12"
    },
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git://github.com/codetunnel/mongoose-auto-increment.git"
    },
    "scripts": {
        "test": "node_modules/mocha/bin/mocha"
    },
    "version": "5.0.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module mongoose-auto-increment](#apidoc.module.mongoose-auto-increment)
1.  [function <span class="apidocSignatureSpan">mongoose-auto-increment.</span>initialize (connection)](#apidoc.element.mongoose-auto-increment.initialize)
1.  [function <span class="apidocSignatureSpan">mongoose-auto-increment.</span>plugin (schema, options)](#apidoc.element.mongoose-auto-increment.plugin)



# <a name="apidoc.module.mongoose-auto-increment"></a>[module mongoose-auto-increment](#apidoc.module.mongoose-auto-increment)

#### <a name="apidoc.element.mongoose-auto-increment.initialize"></a>[function <span class="apidocSignatureSpan">mongoose-auto-increment.</span>initialize (connection)](#apidoc.element.mongoose-auto-increment.initialize)
- description and source-code
```javascript
initialize = function (connection) {
  try {
    IdentityCounter = connection.model('IdentityCounter');
  } catch (ex) {
    if (ex.name === 'MissingSchemaError') {
      // Create new counter schema.
      counterSchema = new mongoose.Schema({
        model: { type: String, require: true },
        field: { type: String, require: true },
        count: { type: Number, default: 0 }
      });

      // Create a unique index using the "field" and "model" fields.
      counterSchema.index({ field: 1, model: 1 }, { unique: true, required: true, index: -1 });

      // Create model using new schema.
      IdentityCounter = connection.model('IdentityCounter', counterSchema);
    }
    else
      throw ex;
  }
}
```
- example usage
```shell
...
''''js
var mongoose = require('mongoose'),
    Schema = mongoose.Schema,
    autoIncrement = require('mongoose-auto-increment');

var connection = mongoose.createConnection("mongodb://localhost/myDatabase");

autoIncrement.initialize(connection);

var bookSchema = new Schema({
    author: { type: Schema.Types.ObjectId, ref: 'Author' },
    title: String,
    genre: String,
    publishDate: Date
});
...
```

#### <a name="apidoc.element.mongoose-auto-increment.plugin"></a>[function <span class="apidocSignatureSpan">mongoose-auto-increment.</span>plugin (schema, options)](#apidoc.element.mongoose-auto-increment.plugin)
- description and source-code
```javascript
plugin = function (schema, options) {

  // If we don't have reference to the counterSchema or the IdentityCounter model then the plugin was most likely not
  // initialized properly so throw an error.
  if (!counterSchema || !IdentityCounter) throw new Error("mongoose-auto-increment has not been initialized");

  // Default settings and plugin scope variables.
  var settings = {
    model: null, // The model to configure the plugin for.
    field: '_id', // The field the plugin should track.
    startAt: 0, // The number the count should start at.
    incrementBy: 1, // The number by which to increment the count each time.
    unique: true // Should we create a unique index for the field
  },
  fields = {}, // A hash of fields to add properties to in Mongoose.
  ready = false; // True if the counter collection has been updated and the document is ready to be saved.

  switch (typeof(options)) {
    // If string, the user chose to pass in just the model name.
    case 'string':
      settings.model = options;
    break;
    // If object, the user passed in a hash of options.
    case 'object':
      extend(settings, options);
    break;
  }

  if (settings.model == null)
    throw new Error("model must be set");

  // Add properties for field in schema.
  fields[settings.field] = {
    type: Number,
    require: true
  };
  if (settings.field !== '_id')
    fields[settings.field].unique = settings.unique
  schema.add(fields);

  // Find the counter for this model and the relevant field.
  IdentityCounter.findOne(
    { model: settings.model, field: settings.field },
    function (err, counter) {
      if (!counter) {
        // If no counter exists then create one and save it.
        counter = new IdentityCounter({ model: settings.model, field: settings.field, count: settings.startAt - settings.incrementBy
 });
        counter.save(function () {
          ready = true;
        });
      }
      else {
        ready = true;
      }
    }
  );

  // Declare a function to get the next counter for the model/schema.
  var nextCount = function (callback) {
    IdentityCounter.findOne({
      model: settings.model,
      field: settings.field
    }, function (err, counter) {
      if (err) return callback(err);
      callback(null, counter === null ? settings.startAt : counter.count + settings.incrementBy);
    });
  };
  // Add nextCount as both a method on documents and a static on the schema for convenience.
  schema.method('nextCount', nextCount);
  schema.static('nextCount', nextCount);

  // Declare a function to reset counter at the start value - increment value.
  var resetCount = function (callback) {
    IdentityCounter.findOneAndUpdate(
      { model: settings.model, field: settings.field },
      { count: settings.startAt - settings.incrementBy },
      { new: true }, // new: true specifies that the callback should get the updated counter.
      function (err) {
        if (err) return callback(err);
        callback(null, settings.startAt);
      }
    );
  };
  // Add resetCount as both a method on documents and a static on the schema for convenience.
  schema.method('resetCount', resetCount);
  schema.static('resetCount', resetCount);

  // Every time documents in this schema are saved, run this logic.
  schema.pre('save', function (next) {
    // Get reference to the document being saved.
    var doc = this;

    // Only do this if it is a new document (see http://mongoosejs.com/docs/api.html#document_Document-isNew)
    if (doc.isNew) {
      // Declare self-invoking save function.
      (function save() {
        // If ready, run increment logic.
        // Note: ready is true when an existing counter collection is found or after it is created for the
        // first time.
        if (ready) {
          // check that a number has already been provided, and update the counter to that number if it is
          // greater than the current count
          if (typeof doc[settings.field] === 'number') {
            IdentityCounter.findOneAndUpdate(
              // IdentityCounter documents are identified by the model and ...
```
- example usage
```shell
...
var bookSchema = new Schema({
    author: { type: Schema.Types.ObjectId, ref: 'Author' },
    title: String,
    genre: String,
    publishDate: Date
});

bookSchema.plugin(autoIncrement.plugin, 'Book');
var Book = connection.model('Book', bookSchema);
''''

That's it. Now you can create book entities at will and they will have an '_id' field added of type 'Number' and will automatically
 increment with each new document. Even declaring references is easy, just remember to change the reference property's type to '
Number' instead of 'ObjectId' if the referenced model is also using the plugin.

''''js
var authorSchema = new mongoose.Schema({
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
