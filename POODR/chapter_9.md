[back](README.md)

# Chapter 9: Testing

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
    @trainer.verify
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
