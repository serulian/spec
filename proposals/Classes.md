## Classes

Classes will provide one of the basic type building blocks in the project.

### Basic Definition

A `class` is a type which provides a container for holding data (variables/fields) and operational
members (methods, properties, indexers, operators, static functions, etc).

### Defining a class

A class is defined with the `class` keyword, along with its *members*.

```
class SomeClass {
   var<int> someInt1 = 2
   var<int> someInt2
   var<int?> someInt3
   
   constructor WithInts(int a, int b, int? c) {
	 this.someInt1 = a
	 this.someInt2 = b
	 this.someInt3 = c
   }

   property<int> SumOfInts {
	 get {
	   return someInt1 + someInt2 + (someInt3 ?? 0)
	 }
   }
} 
```

### Supported class members

The full list to be determined, but initially:

- constructor
- variable (field)
- functions
- properties
- operators


### `constructor`: Constructing classes

A class is constructed by invoking a `constructor` defined on the class: 

```
SomeClass.SomeConstructor(firstarg, secondarg)
```

If a class has no constructors defined, a *default constructor* named `new` is defined. This constructor
is *module private* and returns a new instance of the class with all fields filled with their "zero" values.


#### Defining constructors

Constructors are defined using the `constructor` keyword:

```
constructor WithDefaultValue() {
  someInt = 1
}

constructor WithValue(int value) {
  someInt = 2
}
```

Constructors can take zero (or more) parameters, which are passed into the constructor. Constructors
as well have an implicit `this` keyword (like all non-static class members), which can be used to
set the initial state of the constructed class.


### `var`: A variable/field

A variable in a class holds some data associated with the *instance* of that class. If a `var` is marked
as `static`, then the value is associated with the *type* of the class.

#### Defining variables

```
var<int> someInt
var<int?> someNullableInt
var<string> someString

static var<int> someStaticInt
```

#### Variable initialization

When a class is constructed, all `var`s in the class by default receive the "zero" value of their declared
type. For nullable types (those ending in `?`), the value is `null`. Otherwise, the `new` constructor (if defined)
is called. If no `new` constructor is defined on the type of the variable being constructed, the variable **must** be nullable
**or** filled in by **all** constructors on the class itself.


### `function`: A function or method

A function in a class executes some action, either on the instance of the class or globally (if marked `static`). If
a function is not marked `static`, the `this` implicit value inside the scope of function provides access to the instance of the class.

#### Defining functions

```
function<void> ReturnsVoid() {
  this.someInt = 2
}

function<int> ReturnsInt(int a) {
  return this.someInt + a + 1
}

static function<bool> DoSomething() {
  return true
}
```

### `property`: A property

A property defines a named field on the class which executes logic on `get` (and optionally, `set`).
If a property is not marked `static`, the `this` implicit value inside the scope of property provides access to the instance of the class.

Properties must have a `get`, but `set` is optional (if the property is treated as a read-only or an inferred value).

Inside a `set` block, an implicit `value` value is added with the value specified in the set expression.

Properties **cannot** be of type `void`.

#### Defining properties

```
property<int> SomeInt {
  get { return this.someInt }
}

property<int> SomeOtherInt {
  get { return this.someInt2 }
  set { this.someInt2 = value }
}
```


### `operator`: An operator

An operator defines how an operator (+, -, ==, etc) works over a class of that type.

Operators are always treated as **static** (even though they don't have the `static` keyword).

Binary Operators must always take in two parameters, each a nullable value of the parent class,
representing the left and right hand side of the expression.

Unary Operators must always take in one parameter, a nullable value of the parent class. 

#### Defining operators

```
operator plus (SomeClass? first, SomeClass? second) {
  first = first ?? SomeClass.WithValue(0)
  second = second ?? SomeClass.WithValue(0)
  return SomeClass.WithValue(first.someInt + second.someInt)
}

operator not (SomeClass? value) {
  return SomeClass.WithValue((value?.someInt * -1) ?? 0)
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
allow for code use and implict [**interfaces**](Interfaces.md) to provide for sharing and substitution
of like-kind types.

Structural subtyping allows one class to share the code of one (or more) "parent" classes:

```
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

```
class ParentClass {
  function<int> ReturnValue() {
    return 2
  }
}

class UsefulClass : ParentClass {
  function<int> ReturnValue() {
    return 45
  }  
}

var usefulClass = UsefulClass.new()
var value = usefulClass.ReturnValue() // Returns 45
```

To access the *shadowed* member, a **cast** is used:

```
var usefulClass = UsefulClass.new()
var value = usefulClass.ReturnValue() // Returns 45
var otherValue = usefulClass.(ParentClass).ReturnValue() // Returns 2
```


The ordering of shadowing is:

- A name defined in the class itself
- The left-most parent class's defined names, moving rightward


#### Subtyping rules

A structurally "deriving" class **cannot** be used in place of a parent:

```
var<ParentClass> someParent = UsefulClass.new() // ERROR! Not allowed!
```

Instead, [interfaces](Interfaces.md) are used to allow for common and shared implementations:

```
interface IHasReturnValue {
  function<int> ReturnValue()
}

function<int> DoSomething(IHasReturnValue instance) {
  return instance.ReturnValue()
}

DoSomething(ParentClass.new())
DoSomething(UsefulClass.new())

```

Since interfaces are **implicit**, no explicit declaration of implementation is needed and their use
ensures that **only the needed method** is available at the call site. This ensures the principle of
least suprise, as the rest of the class's implementation details are completely hidden to the called
method.


#### Constructing subtype classes

Given a set of classes that both define constructors, the "deriving" class **must** call one of the
parent's constructors to ensure proper initialization, by using a variation of the casting syntax:

```
class IntHolder {
  var<int> someInt

  constructor WithValue(int a) {
	this.someInt = a 
  }
}

class BoolHolder {
  var<bool> someBool

  constructor WithValue(bool a) {
	this.someBool = a 
  }
}

class IntBoolHolder : IntHolder + BoolHolder {
  constructor WithValues(int a, bool b) {
    this.(IntHolder).WithValue(a)
    this.(BoolHolder).WithValue(b)
  }
}
```

Failure to call one (and exactly one) `constructor` of a parent class will result in a semantic
error. If a parent class does not explicitly define constructors (and thus, the `new` constructor)
is available, it will be called implicitly *if accessible to this module*. Otherwise, an error is raised.


### Working with types

Information on working with classes and interfaces can be found in the [Type Operations](TypeOperations.md) proposal.