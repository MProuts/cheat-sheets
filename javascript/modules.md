[back](README.md)

# Modules

## IIFEs
Stands for 'Immediately-Invoked Function Expressions', this was originally how
modules were written in Javascript.

#### app.js

```js
APP = {}
```

#### module_1.js
```js
(function() {
  var foo = 'foo'

  function bar(baz) {
    console.log(baz)
  }

  // export
  APP.bar = bar
})()
```

#### module_2.js
```js
(function() {
  // import
  var bar = APP.bar

  bar()
})()
```

## Common JS
An improvement over IIFEs. Comes baked into Node.js, but you need to use a bundler like
webpack to use with browsers


#### module_1.js
```js
var foo = 'foo'

function bar(baz) {
  console.log(baz)
}

// export
module.exports = {
  bar: bar
}
```

#### module_2.js
```js
// import
var bar = require(./module_1.js).bar

bar()
```

## ES6 Modules
Baked into the Javascript language specification since ES6, so you don't need to
do anything extra to use

#### module_1.js
```js
var foo = 'foo'

export function bar(baz) {
  console.log(baz)
}
```

#### module_2.js
```js
import { bar } from './module_1'

bar()
```
