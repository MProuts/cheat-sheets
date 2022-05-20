[back](README.md)

# Chapter 8: Composition

- Composition defines a **has-a** relationships as opposed to inheritance which
  defines a **is-a** relationship
- In UML class diagrams, **black diamond** represent composition, **number**
  represents number of parts.
- When you have a pair of classes, one plural, one singular (e.g. `part` and
  `parts`) you can add "object" when discussing so you don't confuse "parts"
  meaning a collection of `part` objects and "parts" a `parts` object.
- To add array-like behavior to a class:

```ruby
class Foos
  extend Forwardable
  def_delegators :@foos_array, :size, :each
  include Enumerable

  def bars
    select { |foo| foo.bar? }
  end
end
```

- It's ok to store object configuartion tersly in tuples that require knowlege of
  the order of the data if that dependency is isolated in factory. You should
  then only create objects using that factory.
- **Use Struct or OpenStruct to replace classes that don't hold any behavior**.
  - OpenStruct take a hash for initialization and derives attributes from the
    provided hash keys.
- Objects **think of objects contained through composition as roles**, not
  specific classes (e.g. to a bike, a sprocket is anything that plays the role
  of a sprocket)
- Inheritance vs. Composition vs. Duck Types:
  - Inheritance:
    - Define **is-a** relationships
    - Good for small sets of real-world objects that fall naturally into static,
      transparently obvious specialization heirarchies where variants are
      more alike than different
    - For the cost of arranging objects in a **rigid heirarchy**, you get
      **automatic message delegation** for free
  - Composition:
    - Define **has-a** relationships
    - Tends toward producing many small objects that have a straighforward
      responsibilities
    - But the way the small pieces interact as a whole may be harder to
      understand
    - For the cost of **explicit message delegation**, you get the **flexibility** of no
      rigid heirarchy
    - Objects contained through composition are treated as **duck types**, but not all duck
      types are contained through composition
    - Often composed objects receive contained objects through dependency injection
  - Duck types:
    - Define **behaves-like-a** relationships
    - Represent **roles** through a common interface
    - Can share behavior through **modules** if needed
- **Prefer composition over inheritance** unless you have a good reason not to
