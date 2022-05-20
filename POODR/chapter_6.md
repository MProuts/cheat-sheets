[back](README.md)

# Chapter 6: Inheritance

- Use **Inheritance** when you need numerous specializations of a stable, common
  abstraction (e.g. Number, Integer, Float)
  - Subclasses share a lot of behavior but differ across some dimensions
  - Subclasses are everything their parents are, plus more
  - Superclasses _are_ the abstraction -- their behavior is extrapolated across
    many subclasses
  - _Classical inheritance_ is a play on the word _class_, not something about
    antiquity
- Think of inheritance as a mechanism for **automatic message delegation**
  from subclasses to superclasses
- **An if statement that checks an attribute that holds the category of self**
  (e.g. checking a `type` or `category` attribute) might indicate the presence of undefined
  subclasses
- When you don't explicitly set a new class's superclass, Ruby automatically
  sets it to `Object`
- **UML class diagrams** can help illustrate class relationships
- When refactoring into an inheritance heirarchy, **always start by pushing
  everything down into a subclass, and then promote abstractions**
  - It's easier to identify and promote abstractions than it is to identify and
    demote concretions
  - It's easier to recover from an extra concretion than an extra abstraction
- It's easier to identify the correct abstraction for the superclass if you **wait
  until you have at least three concrete subclasses**.
- **Use template methods to invite subclasses** to provide specializations to the
  algorithm provided by the superclass
- **Throw helpful error message from abstract template methods** like this:

```ruby
def abstract_template_method
  raise NotImplementedError, "This #{self.class} cannot respond to:"
end
```

- **Use hook messages instead of calling #super()** from subclasses.
  - This puts the method's whole algorith in the superclass, meaning it's easier
    to reason about and you can change the algorithm without needing to change
    how the subclasses contribute to it.
  - Subclasses can remain agnostic about the whole algorithm -- they just know
    about their own individual contribution.
- **Keep heirarchies shallow** -- the hook message pattern falls apart if you
  add another level to the heirarchy

```ruby
class SuperClass
  def initialize
    @foo = "foo"

    post_initialize
  end

  def config
    { foo: "foo" }.merge(local_config)
  end
end

class SubClass
  def post_initialize
    @bar = "bar"
  end

  def local_config
    { bar: "bar" }
  end
end
```

Liskov Substitution Principle

- Subtypes must be substitutible for their supertypes
