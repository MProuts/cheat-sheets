[back](README.md)

# Chapter 5: Duck Types

- **Duck types** are virtual across-class type defined by **what** the members do,
  **not who** exactly they are
- Duck types replace dependencies on specific classes with dependencies on a set of
  messages (or interface) -- makes them interchangeable
- In Ruby, a single class can implement several different duck types (just has
  to implement both sets of messages)
- In defining duck type's interface, focus on what the caller wants, not how
  the implementer does it
- **#kind_of?, #is_a?,#responds_to?, and case statements that switch on
  class** usually indicate the presence of an unidentified duck
  - You're reaching into another class to tell it _how_ to do _what_ you want,
    instead of just sending a common message
  - An exception to this rule is that it's usually safe to explicitly depend
    on stable core Ruby classes like Integer or Hash (Rails frequently
    checks the class of the argument passed to handle receiving a array vs. an
    integer for example.)
- Duck types in Ruby exist as implicit agreements between classes to share a
  common interface. Because they aren't explicitly defined in code, it's
  important to document them with tests (see Chapter 9).
- Statically-typed languages that allows casting aren't actually type safe -
  zing!
