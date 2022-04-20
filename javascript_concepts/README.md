[back](/README.md)

# Javascript Concepts

## Scope
- **Hoisting**: variables are assigned default value of `undefined` on interpreter’s
  first pass through a file (creation phase)
- **Scopes**: function invocations create their own execution contexts (within
  global execution context), variables are looked up from inner context out
- **Closures**: when a function is created inside of another function, a copy of
  the function’s execution context is preserved for future invocations of the inner
  function — this is called a closure context

## Binding
- **Implicit**: `this` is bound to the object to the left of the dot in the method invocation
- **Explicit**:

```js
// Fn.call() binds `this` to obj
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

## Event Loop
- Here's a great [explainer video](https://www.youtube.com/watch?v=8aGhZQkoFbQ).
- Javascript consists of a **heap**, a **call stack**, **web APIs** that the browser
  provides, a **job queue**, and a **task queue**
- Javascript is single threaded runtime, which means it has one **call stack**
    per process
- **Web APIs** let you trigger stuff on browser threads from the JS thread
- When async web API calls complete, they enqueue their callbacks on the
  **task queue**
- When async web API calls wrapped in Promises complete, they enqueue their
  callbacks on the **job queue**
- The **event loop** sits between the queues and the call stack, dequeues next callback onto
  call stack when the call stack is empty.
    - Job queue callbacks a executed before task queue callbacks

## Asynchronous Code
### Events
  - The Javascript runtime is single threaded
  - **Web APIs** let you do stuff on browser threads from the JS thread
      (e.g. listening for a click, performing a network request)
  - **Events** are how the **Web APIs** notify the JS thread about changes
      on browser threads (e.g. a click happened, network request is complete)
  - Events are associated with specific **event targets** (e.g. DOM element, XHR request)
### Callbacks
  - You can add a callback to an event target natively using `addEventListener`,
    the callback gets enqueued in the **task queue** every time the event fires
  - Third-party functions that use Browser APIs usually accept callbacks as
    arguments (e.g. `$.JSON`)
  - Drawbacks:
    - Sequential async functions lead to deeply embedded code that's hard to
        follow -- "callback hell"
    - Inversion of control: you're forced to let third-party API decide how/when
      to enqueue your callbacks
### Promises
  - Here's a [Deep Dive](https://exploringjs.com/deep-js/ch_implementing-promises.html)
  - A Promise object is returned synchronously from an asynchronous function in one of 3
    possible states: ‘pending’, ‘fulfilled’, ‘rejected’
  - Constructor takes **callback** with two functions as arguments: **resolve** and **reject**
  - Inside that callback, the async function can decide when/how to update the returned Promise's status:
      - to  ‘fulfilled’ by calling the passed-in `resolve(value)` function
      - to ‘rejected’ by calling the passed-in `reject(reason)` function
  -  The calling code can specify what to do when the returned promise's status changes:
      - to ‘fulfilled’ by passing callback to `then((value) => {})` method
      - to ‘rejected’  by passing callback to `catch((reason) => {})` method
  - `then` and `catch` return promises — enables chaining
  - only one `catch` is needed for the chain
  - Benefits:
    - Flattens code, makes it easier to follow steps sequentially
    - Error handling consolidated in one place
    - Caller retains control over how callbacks are executed\*\*\*

\*\*\* This is because the browser thread notifies the runtime thread about changes by ***updating a state object's
status***, rather than directly ***enqueuing a callback***

### Async/Await
  - Lets you write asynchronous code as you would write synchronous code by adding
      annotations
  - Annotate asynchronous functions with keyword **async**
  - Within annotated **async** functions, mark calls to web APIs or other
      asynchronous functions with keyword **await**
  - Runtime will wrap functions annotated with **async** in a promise and return it immediately when the function is called
  - Runtime will pause on function calls anotated with **await** and continue
      normal execution when those finish
      - NOTE: I'm guessing this is implemented with another promise under the hood, so it's non-blocking
  - You need to wrap async function body in `try/catch`, or use `catch()` from
      caller
  - Caller needs to use `then()` to access the value
