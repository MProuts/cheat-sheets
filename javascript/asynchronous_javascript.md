[back](README.md)

# Asynchronous Javascript

## Event Loop
- Here's a great [explainer video](https://www.youtube.com/watch?v=8aGhZQkoFbQ).
- The Javascript runtime consists of a **heap**, a **call stack**, **web APIs**
  that the browser provides, a **job queue**, and a **task queue**
- Javascript is a single threaded runtime, which means it has one **call stack**
- **Web APIs** let you execute stuff on browser threads from the JS thread
- When a web API call completes (or an event is triggered) on the browser thread, it enqueues its callback
    function(s) onto the **task queue**
- When a web API call wrapped in Promises completes (or an event is triggered) on the browser thread, it enqueues its
    callback function(s) onto the **job queue**
- The **event loop** sits between the queues and the call stack, dequeueing the
    next callback function and pushing it onto the call stack whenever  the call
    stack is empty.
    - The event loops drains the job queue before drawing from the task queue

## Events
  - The Javascript runtime is single threaded
  - **Web APIs** let you execute stuff on browser threads from the JS thread
      (e.g. listening for a click, performing a network request)
  - **Events** are how the **Web APIs** notify the JS thread about changes
  - Events are associated with specific **event targets** (e.g. DOM element, XHR request)
  - You can add a callback to an event target natively using `addEventListener`,
    the callback gets enqueued in the **task queue** every time the event fires

## Callbacks
  - Callbacks let you do something with the results of an async call
    when it eventually finishes on the browser thread by passing in a function
  - When the callback is a regular function, `this` in the callback is usually
    bound to the event target (e.g. the button that was clicked)
  - When the callback is an arrow functions, `this` in the callback will refer
    to the object on which the function was created (or window)
  - Third-party functions that use Browser APIs usually accept callbacks as
    arguments (e.g. `$.JSON`)
  - Drawbacks:
    - Sequential async functions lead to deeply embedded code that's hard to
        follow -- i.e. "callback hell"
    - You're forced to let third-party API decide how/when
      to enqueue your callbacks -- i.e. "inversion of control"
## Promises
  - Here's a [Deep Dive](https://exploringjs.com/deep-js/ch_implementing-promises.html)
  - A Promise object is returned synchronously from an asynchronous function in one of 3
    possible states: ‘pending’, ‘fulfilled’, ‘rejected’
  - The Promise constructor takes **callback** with two functions as arguments: **resolve** and **reject**
  - Inside that callback, the writer of the asynchronous function can decide when/how to
    update the returned Promise's status:
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

\*\*\* This is because the browser thread notifies the runtime thread about changes by ***updating an object's
status***, rather than directly ***enqueuing a callback***

## Async/Await
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
  - Caller needs to chain `then()` onto asynchronous function call to access the value
