[back](README.md)

# Chapter 2: Single Responsibility

- Classes should have a single responsibility, to check this:
  - To test this, **rephrase each of a class's methods as a question** and see
    if the question makes sense. For example, “Please Ms. Order, what is your status?”
  - Try to describe what the class is doing in one sentence e.g. “Calculate the
    ratio between two toothed sprockets”
- Methods should have a single responsibility too
  - E.g. iteration and operation can be separate methods
- **Put off making design decisions** until you have more information
  - E.g. tolerate duplication, allow classes to have too much responsibility,
    refactor when more of the picture comes into view
- Depend on behavior, not data
  - **Wrap instance variables in methods.** If you later need to change/transform
    this data (e.g. adjust by some factor) you have a layer of indirection and
    can make that change in one place in the wrapper method. “Data very often
    has behavior that you don’t yet know about. Send messages to access
    variables, even if you think of them as data.”
  - **Use a `Struct` to wrap complexly structured data** so you can send
    messages and not depend on the structure of the data. If the structure of
    the data changed, you would just need to update the
    struct in one place, but all callers would still work.
