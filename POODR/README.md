[back](/README.md)

# POODR Cheatsheet

## Chapter 1: Object Oriented Design

- Acronym - SOLID:
  - **S**ingle Responsibility
  - **O**pen-Closed
  - **L**iskov Substitution
  - **I**nterface Segregation
  - **D**ependency Inversion

## Chapter 2: Single Responsibility

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

## Chapter 3: Dependencies

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

## Chapter 4: Interfaces

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

## Chapter 5: Duck Types

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

## Chapter 6: Inheritance

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

## Chapter 7: Modules

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

## Chapter 8: Composition

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

## Chapter 9: Testing

- If a test requires painful setup, the code expects too much context.
- If testing one object drags a bunch of others into the mix, the code has too
  many dependencies.
- **Think of tests as additional application objects** that depend on existing
  classes
  - Limit dependencies, rely on stable ones
- Concentrate on incoming and outgoing messages
  - **Test messages that return values**. These are tests of **state**.
  - Don't test outgoing messages that don't produce side-effects. These are called **queries**.
  - **Test messages that do have side-effects**. These are called **commands**.
- BDD takes an outside-in approach, TDD takes an inside-out approach
- Tests should remain as ignorant as possible about everything except the **object
  under test**, and only know about the messages that come and go at the edge of
  the object.

### Testing Composed Objects

- **Use doubles to mimic injected role/duck objects in tests**.
  - Stub out only those methods needed for the test

```ruby
class DiameterDouble
  def diameter
    10
  end
end
```

- **Treat doubles like first-class role members and test them**
  - If the interface of the role changes, we may forget to update the double

```ruby
class DiameterDoubleTest < MiniTest::Unit::TestCase
  include DiameterizableInterfaceTest

  def setup
    @object = DiameterDouble.new
  end
end
```

### Testing Private Methods

- An object with many private methods probably has too many responsibilities
- **Don't write tests for private methods** in most circumstances
  - They might be helpful around complex private code

### Testing Commands

- **Test commands using mocks**
- Reminder: commands are external message sends that cause side-effects
- **Only return results from mocks when it's necessary to get the test to run**
  - Mocks are meant to prove that the messages get sent, the test shouldn't do
    anything fancy with the return value

```ruby
class DogTest
  include DogInterfaceTest

  def setup
    @trainer = MiniTest::Mock.new
    @object = Dog.new(trainer: @trainer)
  end

  def test_notifies_observers_when_performs_trick
    @trainer.expect(:did_trick, true, 'play dead')
    @dog.do_trick('play dead')
    @observer.verify
  end

end
```

### Testing Roles

- In MiniTest, you can **use Ruby modules to share tests between role players**

```ruby
module RolePlayerInterfaceTest
  def test_implements_role_player_interface
    assert_respond_to(@object, :foo)
  end
end

class FooTest < MiniTest::Unit::TestCase
  include RolePlayerInterfaceTest

  def setup
    @object = Foo.new
  end
end

class BarTest < MiniTest::Unit::TestCase
  include RolePlayerInterfaceTest

  def setup
    @object = Bar.new
  end
end
```

- You should also **test that commands get sent to role players** from composed
  objects when relevant.

```ruby
class BazTest < MiniTest::Unit::TestCase
  def tests_command_is_sent
    @player = MiniTest::Mock.new
    @baz = Baz.new
    @player.expect(:foo, nil, @baz)

    @baz.play(@player)
    @player.verify
  end
end
```

### Testing inheritance heirarchy interfaces

- **Test that superclass interface is implemented** in a module \<Superclass\>InterfaceTest
- **Test that subclass interface is implemented** in a module \<Superclass\>SubclassTest
  - The **subclass interface** is defined by the set of abstract methods on the
    superclass
- **Test that the superclass raises an error if abstract methods are called**:

```ruby
module DogInterfaceTest
  def tests_responds_to_color
    assert_respond_to(@object, :color)
  end
end

module DogSubclassTest
  def test_respond_to_breed
    assert_respond_to(@object, :breed)
  end
end

class DogTest
  include DogInterfaceTest

  def setup
    @object = Dog.new
  end

  def test_forces_subclasses_to_implement_breed
    assert_raises(NotImplementedError) { @dog.breed }
  end
  ...
end

class HuskyTest
  include DogInterfaceTest
  include DogSubclassTest

  def setup
    @husky = @object = Husky.new
  end
  ...
end
```

### Testing Template Methods

- Note that this tests the actual implementation of `breed` whereas
  `DogSubclassTest` above was just testing for the existence of the method

```ruby
class HuskyTest
  include DogInterfaceTest
  include DogSubclassTest

  def setup
    @husky = @object = Husky.new
  end
  ...

  def test_specifies_breed
    assert_equal('husky', @husky.breed)
  end
end
```

### Testing Hook Methods

- **Test that subclasses implement hook method to provide specialization**

#### From Subclass

```ruby
class HuskyTest
  include DogInterfaceTest
  include DogSubclassTest

  def setup
    @husky = @object = Husky.new
  end
  ...

  def specifies_local_tricks
    assert(@husky.local_tricks.includes?('beg'))
  end
end
```

#### From Superclass

- **Use stubbed subclass to test that specializations make it into the restuls of the
  algorithm on the superclass**
- **Test that the stubbed subclass conforms to the subclass interface** so that
  it stays up to date and doesn't give us false passes
- You can use a stubbed subclass to prove abstract methods must be implemented
  -- this is useful when it requires a lot of setup to create an instance of an
  abstract superclass

```
class BlankBreed < Dog; end

class StubbedBreed < Dog
  def local_tricks
    'play dead'
  end
end

class StubbedBreedTest < MiniTest::Unit::TestCase
  include DogSubclassTest

  def setup
    @object = StubbedBreed.new
  end
end

class DogTest
  def setup
    @blank_breed = BlankBreed.new
    @stubbed_breed = StubbedBreed.new
  end

  def test_forces_subclasses_to_implement_breed
    assert_raises(NotImplementedError) { @blank_breed.breed }
  end

  def test_includes_local_tricks_in_tricks
    assert(@stubbed_breed.tricks.includes?('play dead'))
  end

  ...
end
```
