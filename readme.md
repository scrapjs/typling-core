# typling

> Create and verify comment types on Esprima-style ASTs

**Notice:** See [`typling-cli`](https://www.npmjs.com/package/typling-cli) if you want a tool instead of a library.  This is the back-end to other typling tools.

```js
var esprima = require('esprima')
var typling = require('typling')

var node = esprima.parse(
  `// Number, Number -> Number
   function foo (x, y) { return x + y }
   foo(123, 'hello world')`,
   { attachComment: true }
 )

var types = typling.create(node)
var report = typling.verify(node, types)
// [ TypeError { ... } ]
```

(Requires comments to be attached to the nodes with `attachComment: true` or similar)

Typling takes Esprima-style ASTs (See [ESTree](https://github.com/estree/estree)) can do 3 things of your choosing:

 - `create`: Scans nodes for signatures (e.g. `// Number -> String`) and returns an array of types that point back to the node they came from.
 - `verify`: Verify some types (probably from `create`) against a node tree, returning an array of errors to handle.
 - `check`: Shorcut for `create` then `verify` on the same node.  Making an AST type checker basically

## Installation

```sh
$ npm install --save typling
```

## Usage

### `typling.check(node, [types])`

Check tree of nodes for types then type errors.  Returns an array of `TypeError`, otherwise empty.

```js
var node = esprima.parse(`
  // Number, Number -> Number
  function foo (x, y) { return x + y }
  foo(1, 'two')`)

typling.check(node)
// [ { [TypeError: 2nd parameter String should be Number] ... } ]
```

Optionally add some additional types with the second parameter.

### `typling.create(node)`

Create an array of types from a tree of Esprima-style nodes.  These types have a `.target` property pointing to the node they come from.

```js
var types = typling.create(node)
// [ [['Number'], 'Number', target: FunctionDeclaration { ... }]
//   [['String', 'Number'], 'String'], target: ProgramModule { ... }]
```

### `typling.verify(types, node)`

Verify calls in node tree against types.

```js
typling.verify(types, node)
// [ TypeError { ... },
//   TypeError { ... },
//   TypeError { ... } ]
```

### `typling.util`

Small functions for handling Esprima-style ASTs and our `types` array.  You can read [their source](lib/util) to find out more

 - `util.error`: Create friendly, location-aware errors in short ways
 - `util.infer`: Attempt to infer a node's type, optionally give some signatures
 - `util.walk`: Takes various types of nodes, and appends their children into an array
 - `util.query`: Map nodes to types

## License

MIT © [Jamen Marz](https://git.io/jamen)

---

[![version](https://img.shields.io/npm/v/typling.svg?style=flat-square)][package] [![travis](https://img.shields.io/travis/jamen/typling.svg?style=flat-square)](https://travis-ci.org/jamen/typling) [![downloads](https://img.shields.io/npm/dt/typling.svg?style=flat-square)][package] [![license](https://img.shields.io/npm/l/express.svg?style=flat-square)][package] [![follow](https://img.shields.io/github/followers/jamen.svg?style=social&label=Follow)](https://github.com/jamen)

[package]: https://npmjs.org/package/typling
