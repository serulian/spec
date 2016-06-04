## Structs

### Basic Definition

A `struct` is a type which provides a container for holding data that can be serialized over the wire.

### Defining a struct

A struct is defined with the `struct` keyword, along with its fields:

```seru
struct SomeStruct {
	SomeField int
	AnotherField bool?
	NestedField AnotherStruct?
	SliceField []int
}
```

### Structural restrictions

All fields within a `struct` must themselves be either structural or serializable. A type is structural if itself a `struct` or a nominal type deriving from a `struct` or other serializable type.

### Constructng structs

A struct is constructed using the structural new syntax:

```seru
var myStruct = SomeStruct{
	SomeField: 2,
	AnotherField: true,
	NestedField: AnotherStruct{},
	SliceField: []int{1, 2, 3},
}
```

Note that all fields that are non-nullable are **required** when constructing the struct.

### Serialization and Deserialization

Structural types can be serialized and deserialized using any type implementing the [`Stringifier` and `Parser` interfaces](https://github.com/Serulian/corelib/blob/master/serialization.seru), respectively.

An example of such a type in the corelib is `json`:

```seru
var myStruct = SomeStruct{
	SomeField: 2,
	AnotherField: true,
	NestedField: AnotherStruct{},
	SliceField: []int{1, 2, 3},
}

var jsonString = myStruct.Stringify<json>()
var parsed = SomeStruct.Parse<json>()
``` 

On failure to parse, `Parse` will `reject` with the parse error.

### Equality

By default all structural types define an `Equals` operator which compares **structurally** based on the equality of its members.

```seru
struct SimpleStruct {
	OnlyField int
}

function<bool> DoSomething() {
	var first = SimpleStruct{
		OnlyField: 2,
	}

	var second = SimpleStruct{
		OnlyField: 2,
	}

	return first == second // Returns true.
}
```