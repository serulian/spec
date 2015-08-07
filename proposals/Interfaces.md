## Interfaces

Interfaces will provide one of the basic type building blocks in the project.

### Basic Definition

An `interface` is a type which provides an abstracted interface (hence, the name) for working with
one or more concrete types (typically [classes](Classes.md)).

### Defining an interface

Interfaces are defined with the `interface` keyword and represent the accessible members of a `class`:

```
interface ISomeInterface {
  function<void> DoSomething()
  var<string> SomeValue
  property<bool> SomeProperty { get; set; }
}
```

**Note:** We recommend a style of prefixing all interfaces with the letter `I` to distinguish from
classes.


#### Interface Members

Interfaces can contain all the same members as a [class](Classes.md). If an interface member is
marked as `static`, then it operates on the interface itself, rather than an instance of a particular
type. Variables **cannot** be marked as static in an interface.


#### Interface operators

As operators are defined as always being `static`, an `operator` when declared on an interface
must have an *implementation* and operates on instance of that *interface*:

```
interface IAddable {
  var<int> TheValue

  operator plus(IAddable? first, IAddable? second) {
    return SomeConcreteClass.Build(first?.TheValue ?? 0)
  }
}

function<IAddable> Add(IAddable first, IAddable second) {
  return first + second
}
```


#### Interface constructors

Interfaces may contain their own `constructor` definitions, but only if they specify a `default` class:

```
class SomeMatchingClass {
  var<int> someInt

  constructor BuildMe(int a) {
    this.someInt = a
  }
}

interface ISomeInterface default SomeMatchingClass {
   constructor BuildMe(int value)
}
```

The `default` class provides the class to call to construct the interface when the constructor is invoked:

```
var<ISomeInterface> someValue = ISomeInterface.BuildMe(2) // Creates an instance of SomeMatchingClass
```

For other classes to match this interface, **they must as well contain the defined constructor(s)**.


### Implementing Interfaces

Interfaces are implemented **implicitly** by all matching classes (and, interfaces). For example, in the following
code:

```
class FooClass {
  function<int> GetValue {
    return 2
  }
}

class BarClass {
  function<int> GetValue {
    return 3
  }

  function<int> AnotherFunction {
    return 4
  }
}

class BazClass {
  function<string> GetValue {
    return 'hello world'
  }
}

interface IHasValue {
  function<int> GetValue()
}
```

Both `FooClass` and `BarClass` implement `IHasValue`, while `BazClass` does not since it has a `GetValue`
function with an incompatible return type.