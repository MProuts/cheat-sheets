[back](README.md)

# Chapter 7: Modules

- **Roles usually come in pairs**, e.g.:
  - a `preparer` that can respond to `prepare_trip`
  - a `preparable` that responds to any message that any `preparer` might send --
    `bicycles`, `customers`, `vehicles`
- We've seen roles as simple duck types that implement the same methods
- When you need to share behavior across ducks, you can use **modules**
  - **The code in a module must apply to all includers** -- can't apply to just
    some
  - Modules, like classes, share code through **automatic method delegation**
- When refactoring into modules **start by writing shared code in a single
  concrete class** and wiring up module includes to an empty module
  - Once the concrete class is working, you can promote abstract behavior to the
    module to share with peers
- **Use template methods to invite includers** to provide specializations to the
  algorithm provided by the module
- An included module's methods go between the class and its superclass on the lookup chain
- Modules are placed on the lookup chain in the reverse order of how they are
  included.
  - This makes intuitive sense -- methods in last module override
    methods in earlier modules.

Summary of message lookup chain:

1. instance methods on concrete class
2. singleton methods directly on specific instance
3. methods on modules included in class
4. instance methods on superclass
5. modules included on superclass
   <br>...etc...
6. instance methods on Object
7. if the method is not found, `method_missing` is sent to the original receiver
8. lookup starts again with `method_missing` message

**Heuristics for choosing a abstraction technique**:

- When an object that uses a variable like `type` or `category` to decide which
  message to send to self contains two highly related but slightly different
  types, use inheritance
- When the sender checks the class of the receiver to determine which message to
  send, use a duck type to represent the role (makes the different role
  players interchangeable with a shared interface)
- When duck types share behavior, use modules
