## Classes

### Basic Definition

A `class` is a type which provides a container for holding data (variables/fields) and operational members (methods, properties, indexers, operators, static functions, etc).

### Defining a class

A class is defined with the `class` keyword, along with its members.

```seru
class SomeClass {
   var<int> someInt1 = 2
   var<int> someInt2
   var<int?> someInt3
   
   constructor WithInts(int a, int b, int? c) {
      ...
   }

   property<int> SumOfInts {
  	 get {
  	   return this.someInt1 + this.someInt2 + (this.someInt3 ?? 0)
  	 }
   }
} 
```

### Creating class instances

#### Implicit new constructor

All classes have an implicit constructor named `new` defined on the class, which can be invoked only in the defining module:

```seru
class SomeClass {}

SomeClass.new()
```

If a class has non-nullable fields that **do not have default values**, the `new()` constructor takes those members in their defined order:

```seru
class SomeClass {
  var<int> requiredValue
}

SomeClass.new(42)
```

#### Structurally constructing

A class can also be constructed structurally using the following syntax:

```seru
class SomeClass {
  var<int> requiredValue
  var<bool?> optionalValue
}

SomeClass{
  requiredValue: 42,
  optionalValue: true,
}
```

Note: If the class has unexported members, then the structural construction will only be allowed within the class's defining module.

Note: Structural construction can also specify writable property values, which will be set after the class is constructed.


### Class members

#### `constructor`: Constructing classes

A class can be constructed by invoking a `constructor` defined on the class: 

```seru
var someInstance = SomeClass.SomeConstructor(firstarg, secondarg)
```

#### Defining constructors

Constructors are defined using the `constructor` keyword:

```seru
class SomeClass {
  var<int> someInt = 0

  constructor WithDefaultValue() {
    var sc = SomeClass.new()
    sc.someInt = 1
    return sc
  }

  constructor WithValue(int value) {
    var sc = SomeClass.new()
    someInt = 2
    return sc
  }
}
```

Constructors can take zero (or more) parameters, which are passed into the constructor. Constructors must return the newly constructed instance of the class or `reject` on error. The `new()` implicit constructor is typically used to create the new instance of the class before additional members are set or called.


### `var`: A variable/field

A variable in a class holds some data associated with the *instance* of that class. 

#### Defining variables

```seru
var<int> someInt = 42
var<int?> someNullableInt
var<string> someString // This member, as non-nullable, must be initialized as construction time.
```

#### Variable initialization

When a class is constructed, all fields marked with default values (via a ` = somevalue`) will be initialized with that value. All nullable fields without values will be set to `null`. All non-nullable fields without default values will be set by the caller creating the instance of the class, either via the `new()` constructor or via structural instantiation of the class.


### `function`: A function or method

A function in a class executes some action on the instance of the class. The `this` implicit value inside the scope of function provides access to the instance of the class.

#### Defining functions

```seru
function<void> ReturnsVoid() {
  this.someInt = 2
}

function<int> ReturnsInt(a int, b int) {
  return this.someInt + a + b
}

function<T> GenericFunction<T>(someParam T) {
  return someParam
}

function<void> unexportedFunction() {
  reject SomeError.new() // Reject instead of returning.
}
```

### `property`: A property

A property defines a named field on the class which executes logic on `get` (and optionally, `set`).
The `this` implicit value inside the scope of property provides access to the instance of the class.

Properties must have a `get`, but `set` is optional (if the property is treated as a read-only or an inferred value).

Inside a `set` block, an implicit `val` value is added with the value specified in the set expression.

Properties **cannot** be of type `void`.

#### Defining properties

```seru
property<int> SomeInt {
  get { return this.someInt }
}

property<int> SomeOtherInt {
  get { return this.someInt2 }
  set { this.someInt2 = val }
}
```


### `operator`: An operator

An operator defines how an operator (+, -, =, etc) works over a class of that type. The full list of supported operators can be found in the [Operators Table](operators.md).

#### Defining operators

```seru
operator plus (SomeClass first, SomeClass second) {
  return first
}

operator not (SomeClass value) {
  return SomeClass.new()
}
```

### Combining classes (or: why no inheritance?)

[Inheritance](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) as found in
traditional OOP languages presents a number of major issues around code reuse and principles of least
surprise. Multiple inheritance can cause major headaches about reasoning ("diamond pattern") while 
single inheritance can be extremely limiting when attempting to represent a type that operates as
two different "kinds" of objects.

#### Structural Subtyping

For this project, we propose instead a form of **structural** subtyping (somewhat like Go) to
allow for code use and implict [interfaces](interfaces.md) to provide for sharing and substitution
of like-kind types.

Structural subtyping allows one class to share the code of one (or more) "parent" classes:

```seru
class AddClass {
   function<int> Add(int first, int second) {
     return first + second
   }
}

class SubtractClass {
   function<int> Subtract(int first, int second) {
     return first - second
   }
}

class AddSubtractClass : AddClass + SubtractClass {
  // Has both Add and Subtract methods
}
```

#### Shadowing

All members of a class are considered **non-virtual**. When a class "overrides" the name of a
member from a "parent", the new name *shadows* the parent class's name:

```seru
class MyParentClass {
  function<int> ReturnValue() {
    return 2
  }
}

class UsefulClass : MyParentClass {
  function<int> ReturnValue() {
    return 45
  }  
}

var usefulClass = UsefulClass.new()
var value = usefulClass.ReturnValue() // Returns 45
```

To access the *shadowed* member, an explicit name must be used:

```seru
var usefulClass = UsefulClass.new()
var value = usefulClass.ReturnValue() // Returns 45
var otherValue = usefulClass.MyParentClass.ReturnValue() // Returns 2
```


The ordering of shadowing is:

- A name defined in the class itself
- The left-most parent class's defined names, moving rightward


#### Subtyping rules

A structurally "deriving" class **cannot** be used in place of a parent:

```seru
var<ParentClass> someParent = UsefulClass.new() // ERROR! Not allowed!
```

Instead, [interfaces](Interfaces.md) are used to allow for common and shared implementations:

```seru
interface IHasReturnValue {
  function<int> ReturnValue()
}

function<int> DoSomething(IHasReturnValue instance) {
  return instance.ReturnValue()
}

DoSomething(ParentClass.new())
DoSomething(UsefulClass.new())
```

Since interfaces are implicit, no explicit declaration of implementation is needed and their use
ensures that **only the needed method** is available at the call site. This ensures the principle of
least suprise, as the rest of the class's implementation details are completely hidden to the called
method.
