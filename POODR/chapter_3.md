[back](README.md)

# Chapter 3: Dependencies

- An object has a dependency when it knows...
  - **The name of another class.**
  - **The name of a message** that it intends to send to someone other than self.
  - **Arguments** that a message requires.
  - The **order of the arguments** that a message requires.
- Objects should have as few dependencies as possible
  - When objects know too much about each other they become impossible to reuse
  - When tests and code are tightly coupled, you can't change the code without
    rewriting the tests

### Dependency Management Techniques

- **Inject dependencies** -- this allows you to replace class names
  with less specific **duck types**.
- If you can't inject a dependency, **isolate instance creation** in a method so
  the dependency is clear and easier to spot and change. For example:

```ruby
def wheel
  @wheel ||= Wheel.new
end
```

- **Isolate external method sends** in their own methods. This replaces an
  external message send with an internal one. If the external message
  changes, your code only needs to change in one place.

```ruby
def diameter
  wheel.diameter
end
```

- **Pass options hash for initialization** to avoid depending on argument order
  - **Use #fetch to assign defaults** when you need to pass
    **booleans** through an options hash (if you use `||` you won’t be able
    to set attribute to `false` or `nil`)
  - If you can't use options hash (e.g. relying on third-party library) **wrap
    external initialization in factories** that use an options hash
- **Dependency direction** can be reversed. E.g. Wheel can know about Gear or Gear can know about Wheel.
  - Classes should depend on other classes that are less likely to change
    than they are.
  - Duck types are less likely to change than concrete classes. Try to depend
    on the least specific duck type.
- **Duck types** in Ruby are analagous to **interfaces** in statically types
  languages. In Ruby, they can exist implicitly based on which messages a
  variable receives, whereas in static languages, they must be explicitly
  spelled out. Interfaces are more abstract (i.e. less specific) than classes
  because several classes can implement the same interface.
