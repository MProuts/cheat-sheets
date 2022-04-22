[back](README.md)

# Variable Contexts
- **Global context** is at the top level of each file (not within any
    function)
- The interpreter makes two passes through each context:
    - In the **Creation Phase**, all variables are added to current context and assigned
      default value of `undefined`, (this is called **Hoisting**)
    - In the **Execution Phase**,
        - The interpreter executes code line by line, assigning variables values
        - When a function call is encoutnered, a new context is embedded within
          the current context
            - The process starts over inside the called function'd body, starting with the **Creation Phase**...
        - When a variable reference is encountered, the context chain is
            searched from current context out all the way to the global context
        - When the end of a function body is reached, it's execution context
            is removed
- **Lexical Scope**:
    - variables from outer contexts are available to inner contexts (^ because
        the entire context chain is searched on variable lookups)
    - variables from inner context are not available to outer contexts (^ because
        contexts don't exist after a function is finished executing)
- **Closure Scope**: when a function is created inside of another function, a copy of
  the outer function’s execution context (and all parents) is preserved for
  future invocations of the inner function (e.g. if function is returned, or
  passed as an argument, called later). This preserved context is called a **closure**.

## Variable Types

- **no keyword**: context is global -- BAD
- **var**:
    - context is entire function
    - no error when referenced before declared (weird)
    - `undefined` when referencing before declared
- **let**:
    - context confined to block
    - reference error when referenced before declared
    - can redefine
- **const**:
    - context confined to block
    - reference error when referenced before declared
    - can’t redefine (but can mutate value)
