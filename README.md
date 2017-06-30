# ipfs-live-db

> Live CRDT DB over IPFS


# Install

```sh
$ npm install ipfs-live-db --save
```

# Import


In the browser environment, you can either use this library by including it and bundling your app together with it (using browserify or webpack, for instance), or you can

## in Node.js or in a browser with a bundler:

```js
const DB = require('ipfs-live-db')
```

## Using a script tag in a browser

```html
<!-- loading the minified version -->
<script src="https://unpkg.com/ipfs-live-db/dist/browser.min.js"></script>

<!-- loading the human-readable (not minified) version -->
<script src="https://unpkg.com/ipfs-live-db/dist/browser.js"></script>
```

Now you can access this library using the `IpfsLiveDb` on the global namespace. (In this case, replace `DB` on the examples below with `IpfsLiveDb`).

# Instantiate

```js
const db = DB([options])
```

Arguments:
* options (object):
  * `ipfs`: a [js-ipfs options object](https://github.com/ipfs/js-ipfs#advanced-options-when-creating-an-ipfs-node)
  * `store` (string, defaults to `"memory"`): a local store, represented by a string. Can either be:
    * `"indexeddb"`: for in-browser persistence
    * `"leveldb"`: for Node.js persistence
  * `partition` (string, mandatory): the partition this data belongs to. It's used to broadcast new record ids (so trackers can follow (and pin) the global partition state)


# API

## db.define([typeName:String, ]typeDefinition:Object)

Defines and returns a type.

```js
const TodoList = db.define('TodoList', {
  type: 'Array',
  of: {
    name: 'TodoItem'
    type: 'Object'
  }
})

const list = TodoList()
```

You can chain them:

```js
db.define('TodoItem', {
  type: 'Object'
})

const TodoList = db.define('TodoList', {
  type: 'Array',
  of: 'TodoItem' // not necessary if you don't want a CRDT
})
```

or simply nest them:

```js
const TodoItem = db.define({
  type: 'Object'
})

const TodoList = db.define('TodoList', {
  type: 'Array',
  of: TodoItem
})
```

## Built-in types

The basic types that come build-in are 'Map' and 'Array'. The rest is up to you.


## Instantiate

### db.create(type, id:String)

Creates an instance for a given type.

Depending on the base type, it returns either a specized `Map` or `Array` instance (see below).

Example:

```js
const id = 'some id'
const item = db.create('TodoItem', id)
```

## Map

### item.get(key:String)

Returns the value for the key.

### item.set(key:String, value:Any)

Sets the value for that key

### item.delete(key:String)

Deletes a property

### item.keys()

Returns all keys for all values

### item.observe(observer:Function)

The `observer` is called whenever something on this object changes. The observer is called with one sole argument of the Event type, which has the following properties:

* `event.type`: The type of the event. "add" - a new key-value pair was added, "update" - an existing key-value pair was changed, or "delete" - a key-value pair was deleted)
* event.name`: The key of the changed property
* `event.value`:  If event type is either "update" or "add", this property defines the new value of the key-value pair

### item.observeDeep(observer:Function)

Same as .observe, but catches events from all children (if they support `.observeDeep`).

`event.path` specifies the path of the change event.

### item.unobserve(observer:Function)

Deactivates an observer.

## Array

### item.insert(position:Number, contents:Array)

Inserts `contents` at position `position`.

### item.push(contents)

Inserts `contents` at the end of the array.

### item.delete(position, length)

Deletes content. The length parameter is optional and defaults to 1.

### item.toArray()

Returns the complete content as an array.

### item.get(position:Number)

Returns the content at a given position.

### item.observe(observer:Function)

The `observer` is called whenever something on this object changes. The observer is called with one sole argument of the Event type, which has the following properties:

* `event.type`: The type of the event. "insert" - a set of values was inserted, "delete" - a set of values was deleted
* `event.index`: The position the operation occurred at.
* `event.length`: The number of positions affected.
* `event.values`:  The values inserted (if event.type is "insert")
* `event.oldValues`:  The values removed (if event.type is "delete")

### item.observeDeep(observer:Function)

Same as .observe, but catches events from all children (if they support .observeDeep).
`event.path` specifies the path of the change event.

### item.unobserve(observer:Function)

Deactivates an observer.

# License

MIT

## Contribute

Feel free to join in. All welcome. Open an [issue](https://github.com/pgte/ipfs-live-db/issues)!

This repository falls under the IPFS [Code of Conduct](https://github.com/ipfs/community/blob/master/code-of-conduct.md).

[![](https://cdn.rawgit.com/jbenet/contribute-ipfs-gif/master/img/contribute.gif)](https://github.com/ipfs/community/blob/master/contributing.md)

## License

[MIT](LICENSE)
