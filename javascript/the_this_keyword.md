[back](README.md)

# The 'this' Keyword

In Javascript, you can attach functions to objects. When you call a function
attached to an object, `this` will refer to that object.

It's also common to create functions that aren't attached to an object, or to
pass a reference to a function defined on an object to another function. In
these cases `this` depending on the context of where it is eventually called.
If you want to ensure a functions body retains the same `this` as it's passed
around (e.g. passing a callback), you need to use `bind()` or an arrow function.

## Binding
- **Implicit**:
    - **Regular functions**: the `this` keyword refers to the object that calls the function (or window)
    - **Arrow functions**: the `this` keyword refers to the object that the arrow function was defined on (or window)
- **Explicit**:

```js
// Fn.call() binds `this` inside function to obj
someFn.call(obj, arg1, arg2)

// Fn.apply() binds `this` to obj, args passed in array
someFn.apply(obj, [arg1, arg2])

// Fn.bind() returns Fn with given binding and args
someFn.bind(obj, arg1, arg2)
```
- **New**:

```js
// constructors create a new object and bind `this` to it
new Animal(arg1, arg2)
```

