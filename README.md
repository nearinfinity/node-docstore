# docstore

Simple and extendable JSON document storage and retrieval

## Installation

    $ npm install docstore

## Usage

```javascript
var ds = require('docstore');

ds.open('./docs/users', function(err, store) {
  assert(err == null);

  store.save({ name: 'Joe', age: 35 }, function(err, doc) {
    assert(err == null);
    console.log(doc.key); // keys are autogenerated and assigned
  });

  // See below for other methods available on store
});
```

## Opening

The first step to using docstore is to open a store. All other
operations are available from the store returned to you via the callback
passed.

### open(directory, options, callback)

__Arguments__

* directory - the directory to read and write documents to/from
* options - an object containing store options (see below)
* callback(err, store) - callback that will be invoked after opening the
  store

__Example__

```javascript
var ds = require('docstore');

ds.open('./docs/users', function(err, store) {
  assert(err == null);
  assert(store != null);
});
```

__options__

The following options are available when opening a store

* format - the format class to use for serializing and deserializing
  objects. It defaults to the JSON formatter in lib/format/json.js

## Saving

When saving, the store uses the configured format implementation to
first serialize the object. If the object has a `key` property it's
value will be used as the filename. Otherwise, a random filename will be
generated.

### save(obj, callback)

__Arguments__

* obj - the object to save
* callback(err, obj) - callback that will be invoked after opening the
  store. It is passed an error if one occurs. If no error occurs it will
  pass the object as saved (with it's generated key).

__Example__

```javascript
store.save({ name: 'Joe', age: 35 }, function(err, doc) {
  assert(err == null);
  console.log(doc.key); // keys are autogenerated and assigned
});
```

## Retrieving

Any document can be retrieved given its key.

### get(key, callback)

__Arguments__

* key - the key of the object to get
* callback(err, obj) - callback that will be invoked after retrieving the
  document. It is passed an error if one occurs. If no error occurs it will
  pass the document.

__Example__

```javascript
store.get('key12302202', function(err, doc) {
  assert(err == null);
  assert(doc.key === 'key12302202');
});
```

## Removing

There are two methods for removing documents. You can remove them one at
a time or clear all documents from a store at once.

### remove(key, callback)

__Arguments__

* key - the key of the object to remove
* callback(err) - callback that will be invoked after removing the
  document. It is passed an error if one occurs.

__Example__

```javascript
store.remove('key12302202', function(err) {
  assert(err == null);
  console.log('Deleted');
});
```

### clear(callback)

__Arguments__

* callback(err) - callback that will be invoked after removing all
  documents. It is passed an error if one occurs.

__Example__

```javascript
store.clear(function(err) {
  assert(err == null);
  console.log('All deleted');
});
```

## Scanning

Scanning is the method used to find particular documents among a large
collection. While there's only one scan method, it can be used in two
ways. You can either get a single callback with all the results as an
array or get them as a stream of document events.

### clear(callback)

__Arguments__

* filter - a function to be used to determine if a document should be
  returned from the scan
* callback(err) - optional callback that will be invoked for all
  documents that have passed the filter function. If no callback is
  passed to the scan method, an `EventEmitter` is returned instead. It
  emits `document` events for all documents passing the filter function,
  an `error` event if any errors occur, and an `end` event when scanning
  is finished.

__Examples__

```javascript
var filter = function(doc) {
  return doc.key === 'key12302202';
};

// get all results at once
store.scan(filter, function(err, docs) {
  assert(err == null);
  assert(docs.length === 1);
  assert(docs[0].key === 'key12302202');
});

// get results as a stream
var stream = store.scan(filter);
stream.on('document', function(doc) {
  assert(doc.key === 'key12302202');
});
stream.on('end', console.log.bind(null, 'Done'));
stream.on('error', console.error.bind(null));
```

## License

(The MIT License)

Copyright (c) 2012 Near Infinity Corporation

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
