# map-visit [![NPM version](https://badge.fury.io/js/map-visit.svg)](http://badge.fury.io/js/map-visit)  [![Build Status](https://travis-ci.org/jonschlinkert/map-visit.svg)](https://travis-ci.org/jonschlinkert/map-visit)

> Map `visit` over an array of objects.

## Install

Install with [npm](https://www.npmjs.com/)

```sh
$ npm i map-visit --save
```

## Usage

```js
var mapVisit = require('map-visit');
```

## Assign or Merge vs. Visit

Let's say you want to add a `set` method to your application that will:

* set key-value pairs on a `data` object
* extend objects onto the `data` object
* extend arrays of objects onto the data object

**Example using `extend`**

Here is one way to accomplish this using Lo-Dash's `extend`:

```js
var _ = require('lodash');

var obj = {
  data: {},
  set: function (key, value) {
    if (Array.isArray(key)) {
      _.extend.apply(_, [obj.data].concat(key));
    } else if (typeof key === 'object') {
      _.extend(obj.data, key);
    } else {
      obj.data[key] = value;
    }
  }
};

obj.set('a', 'a');
obj.set([{b: 'b'}, {c: 'c'}]);
obj.set({d: {e: 'f'}});

console.log(obj.data);
//=> {a: 'a', b: 'b', c: 'c', d: { e: 'f' }}
```

The above approach works fine for most use cases. But **if you also want to emit an event** each time a property is added to the `data` object. A better approach would be to use `visit`.

**Example using `visit`**

In this approach, when an array is passed to `set`, `mapVisit` calls `set` on each object in the array. When an object is passed, `visit` calls `set` on each property in the object. As a result, the `data` event will be emitted every time a property is added to `data`.

```js
var mapVisit = require('map-visit');
var visit = require('object-visit');

var obj = {
  data: {},
  set: function (key, value) {
    if (Array.isArray(key)) {
      mapVisit(obj, 'set', key);
    } else if (typeof key === 'object') {
      visit(obj, 'set', key);
    } else {
      // some event-emitter
      console.log('emit', key, value);
      obj.data[key] = value;
    }
  }
};

obj.set('a', 'a');
obj.set([{b: 'b'}, {c: 'c'}]);
obj.set({d: {e: 'f'}});
obj.set({g: 'h', i: 'j', k: 'l'});

console.log(obj.data);
//=> {a: 'a', b: 'b', c: 'c', d: { e: 'f' }, g: 'h', i: 'j', k: 'l'}

// events would look something like:
// emit a a
// emit b b
// emit c c
// emit d { e: 'f' }
// emit g h
// emit i j
// emit k l
```

## Related projects

* [collection-visit](https://www.npmjs.com/package/collection-visit): Visit a method over the items in an object, or map visit over the objects… [more](https://www.npmjs.com/package/collection-visit) | [homepage](https://github.com/jonschlinkert/collection-visit)
* [object-visit](https://www.npmjs.com/package/object-visit): Call the given method on each value in the given object. | [homepage](https://github.com/jonschlinkert/object-visit)

## Running tests

Install dev dependencies:

```sh
$ npm i -d && npm test
```

## Contributing

Pull requests and stars are always welcome. For bugs and feature requests, [please create an issue](https://github.com/jonschlinkert/map-visit/issues/new).

## Author

**Jon Schlinkert**

+ [github/jonschlinkert](https://github.com/jonschlinkert)
+ [twitter/jonschlinkert](http://twitter.com/jonschlinkert)

## License

Copyright © 2015 Jon Schlinkert
Released under the MIT license.

***

_This file was generated by [verb-cli](https://github.com/assemble/verb-cli) on October 06, 2015._