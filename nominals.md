## Nominal types

### Basic Definition

A nominal type is a type defining a new name and interface for another existing data type ([class](classes.md), [struct](structs.md) or nominal type).

Nominal types are useful for presenting an existing object via a different interface or adding logic for easier working with another type. Note that nominal types *cannot contain new data members*.

Nominal types are especially useful for wrapping WebIDL types with nicer Serulian interfaces. Examples can be found in the corelib [primitives](https://github.com/Serulian/corelib/blob/master/primitives.seru) module.

### Defining a nominal type

A nominal type is defined used the `type` keyword, along with its members:

```seru
type MyNominalType : SomeClass {
	constructor BuildMe(sc SomeClass) {
		return MyNominalType(sc)
	}

	function<void> DoSomething() {
		return SomeClass(sc).DoSomethingElse()
	}
}
```

### Creating nominal type instances

An "instance" of a nominal type is created by wrapping an instance of its base type. For example, given the nominal type:

```seru
type MyNominalType : SomeClass { ... }
```

creating an instance of this type requires taking an instance of `SomeClass` and wrapping it:

```seru
var sc = SomeClass.new()
var myNominalInstance = MyNominalType(sc)
```

### Nominal type members

A nominal type can contain all members that are found in a [class](class.md) **except** fields. This ensures that the nominal type only operates over the same data as the underlying instance.

