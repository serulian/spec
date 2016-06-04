## Interfaces

### Basic Definition

An `interface` is a type which provides an abstracted interface for working with
one or more concrete types (typically [classes](classes.md)).

### Defining an interface

Interfaces are defined with the `interface` keyword and represent the accessible members of a `class`:

```seru
interface SomeInterface {
  function<void> DoSomething()
  property<bool> SomeProperty
  property<bool> ReadOnlyProperty { get }

  constructor SomeConstructor {
    return SomeClass.new()
  }

  operator Plus(left ISomeInterface, right ISomeInterface) {
    return left
  }
}
```

#### Interface Members

Interfaces can contain all the same members as a [class](Classes.md) except fields. 

#### Interface operators

Static operators when declared on an interface must have an *implementation* and operate on instances of that *interface*:

```seru
interface IAddable {
  operator plus(IAddable first, IAddable second) {
    return first
  }
}

function<IAddable> Add(IAddable first, IAddable second) {
  return first + second
}
```


#### Interface constructors

Constructors in interfaces must have an implementation that either returns a default value or rejects:

```seru
class SomeMatchingClass {
  var<int> someInt

  constructor BuildMe(int a) {
    return SomeMatchingClass{
      someInt: a,
    }
  }
}

class AnotherMatchingClass {
  constructor BuildMe(int a) {
    return AnotherMatchingClass.new()
  }
}

interface ISomeInterface {
   constructor BuildMe(int value) {
     return AnotherMatchingClass.new()
   }
}

function<ISomeInterface> DoSomething<T : ISomeInterface>(value int) {
  return T.BuildMe(value)
}
```

### Implementing Interfaces

Interfaces are implemented **implicitly** by all matching types. For example, in the following
code:

```
class FooClass {
  function<int> GetValue() {
    return 2
  }
}

class BarClass {
  function<int> GetValue() {
    return 3
  }

  function<int> AnotherFunction() {
    return 4
  }
}

class BazClass {
  function<string> GetValue() {
    return 'hello world'
  }
}

interface IHasValue {
  function<int> GetValue()
}
```

Both `FooClass` and `BarClass` implement `IHasValue`, while `BazClass` does not since it has a `GetValue`
function with an incompatible return type.

