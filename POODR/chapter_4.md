[back](README.md)

# Chapter 4: Interfaces

- **_interface_** can refer to two things:
  - An abstract interface, or a structure that defines a set of
    classes that implement the same public interface. In Ruby, these can be duck
    types, abstract superclasses, etc. In static languages these are always
    explicitly created, e.g. with the `interface` keyword.
  - The **public interface** or **private interface** of a class (defined by its public and private
    methods)
- **public methods**
  - read like a list of the class's responsibilities
  - are safe for other objects to depend on because they're are not likely to change
  - are thoroughly tested
- **private methods**
  - hold implementation details intentionally hidden from the outside
  - are unsafe for other objects to depend on because they're very likely to change
  - shouldn't be tested unless there's a good reason (testing them can make the code rigid)
    - isolate private interface tests
- **decide on a messages and _then_ figure out what classes should receive
  them**
  - as opposed to deciding on classes and then figuring out their messages
  - **UML sequence diagrams** can help you prototype message passing and discover
    classes this way
  - this switches the focus onto designing the public interfaces, which define the
    application and are the most critical thing to get right
- Caller should **ask for what** it wants **and
  not how** it wants it done.
  - I.e. the caller should **trust** the receiver to know how.
- **Law of Demeter** -- 'Use only one dot' or avoid method chaining.
  - Using delegation just hides method chains, doesn't actually solve the
    problem.
  - Usually long method chains are a sign that the caller is instructing on **how**
    (reaching through to make distant behavior happen) instead of
    asking for **what** it wants and trusting the receiver to figure out how.
