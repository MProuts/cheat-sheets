[back](README.md)

# The 'this' Keyword

In Javascript, you can attach functions to objects. When you call a function
attached to an object, `this` inside the function will refer to that object.

It's also common to create functions that aren't attached to an object, or to
pass a reference to a function defined on an object to another function. In
these cases `this` will depend on the context of where it is eventually called.
If you want to ensure a function retains the same idea of `this` as it's passed
around (e.g. when passing a callback), you need to use `bind()` or an arrow function.

## Implicit `this` binding
- **Regular functions**: the `this` keyword refers to the object that receives the function call (or window)
- **Arrow functions**: the `this` keyword refers to the object on which the function was defined (or window)

## Explicit `this` binding

```js
// call() - binds `this` inside function to obj and executes the function with
given args
someFn.call(obj, arg1, arg2)

// apply() - same as call() but args can be passed as an array
someFn.apply(obj, [arg1, arg2])

// bind() - returns a new function with `this` bound to obj and args given
newFn = someFn.bind(obj, arg1, arg2)
```
## `this` binding with `new`

Invoking a constructor using `new` binds `this` to a new object based on the
constructor's prototype and returns it after execution.

```js
function Animal(arg1, arg2) {
  // behind the scenes:
  // this = Object.new(Animal.prototype)

  this.arg1 = arg1
  this.arg2 = arg2

  // behind the scenes:
  // return this
}

new Animal(arg1, arg2)
```

