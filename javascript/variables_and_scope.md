[back](README.md)

# Variables & Scope

## Contexts
- The **global context** is at the top level of each file (not within any
    function)
- The interpreter makes two passes through each context:
    - In the **creation phase**, whenever the interpreter encounters...
        - A variable declaration, it adds it to the current context and assigns
          it a default value of `undefined` (this is called **hoisting**)
        - A function definition, it adds it to memory
    - In the **execution phase**, whenever the interpreter encounters...
        - A variable assignment, it updates the variable's value on the current context
        - A variable reference, it searches for the variable through the context chain from the
            current context out to the global context
        - A function call, it creates a new variable context embedded within the current context
            - And the process starts over from the **creation phase** inside the called function's body
        - When the end of a function body is reached, its execution context
            is removed from the context chain
## Scope
- **Variable lookups**: Variable lookups work from the *inside-out* and **not** the *outside-in*.
    - Variables from inner contexts are not available to outer contexts because
      contexts are removed from the context chain after a function is finished
      executing \(see above\)
    - Variables from outer contexts are available to inner contexts because the
      interpreter searches through the entire context chain on variable lookups
      \(see above\)
- **Lexical Scope**:
    - The second item above is sometimes referred to as 'lexical scope' in
      Javascript, but it's actually a specific implementation of lexical
      scope.
    - Lexical scope just means that a variable's scope is determined
      (in some way) by its location in source code, rather than the state of
      the callstack at runtime.
    - Most modern programming languages use lexical scope -- e.g. Ruby uses
      lexical scope, but implements it differently.
- **Closures**: when a function is defined within an another function and a
    reference to the inner function remains after the outer function finishes
    executing (either because the inner function is returned to the caller, or
    passed as an argument to an asynchronous function), a copy of the outer function’s
    context chain is preserved for future invocations of the inner function.
    This preserved context is called a **closure**.

## Declarations

- **<NO KEYWORD\>**:
    - accessible globally
    - avoid this because it pollutes the global namespace
- **var**:
    - when declared inside a block, accessible outside of that block
    - no error when referenced before declared (weird)
    - `undefined` when referencing before declared
- **let**:
    - when declared inside a block, not accessible outside of that block
    - not accessible outside of block even within the same function
    - reference error when referenced before declared
    - can reassign
- **const**:
    - when declared inside a block, not accessible outside of that block
    - reference error when referenced before declared
    - can’t reassign (but can mutate value)
