# Operators

The following is the set operators that can be defined on a [Class](classes.md), [Interface](interfaces.md) or [Nominal Type](nominals.md)

Note: In all type definitions below, `C` represents the definiting type, while other letters indicate that the type defined is up to the implementer, including `any`.

| Operator Name | Usage Syntax                      | Is Static  | Return Type       | Definition                                   |
|---------------|-----------------------------------|------------|-------------------|---------------------------------------------------------------------|
| Plus          | `a + b`                           | Static     | `C` (implicit)    | `operator Plus(left C, right C) { ... }`
| Minus         | `a - b`                           | Static     | `C` (implicit)    | `operator Minus(left C, right C) { ... }`
| Times         | `a * b`                           | Static     | `C` (implicit)    | `operator Times(left C, right C) { ... }`
| Divide        | `a / b`                           | Static     | `C` (implicit)    | `operator Div(left C, right C) { ... }`
| Modulo        | `a % b`                           | Static     | `C` (implicit)    | `operator Mod(left C, right C) { ... }`
|               |                                   |            |                   |                                             |
| Bitwise Xor   | `a ^ b`                           | Static     | `C` (implicit)    | `operator Xor(left C, right C) { ... }`
| Bitwise Or    | `a | b`                           | Static     | `C` (implicit)    | `operator Or(left C, right C) { ... }`
| Bitwise And   | `a & b`                           | Static     | `C` (implicit)    | `operator And(left C, right C) { ... }`
| Left Shift    | `a << b`                          | Static     | `C` (implicit)    | `operator Leftshift(left C, right C) { ... }`
| Right Shift   | `a >> b`                          | Static     | `C` (implicit)    | `operator Rightshift(left C, right C) { ... }`
| Bitwise Not   | `~a`                              | Static     | `C` (implicit)    | `operator Not(value C) { ... }`
|               |                                   |            |                   |                                             |
| Equality      | `a == b` `a != b`                 | Static     | `bool` (implicit) | `operator Equals(left C, right C) { ... }`
| Comparison    | `a < b` `a > b` `a <= b` `a >= b` | Static     | `int` (implicit)  | `operator Compare(left C, right C) { ... }`
|               |                                   |            |                   |                                             |
| Range         | `a .. b`                          | Static     | `C*` (implicit)   | `operator Range(left C, right C) { ... }`
|               |                                   |            |                   |                                             |
| Contains      | `a in b`                          | Instance   | `bool` (implicit) | `operator Contains(item A) { ... }`
|               |                                   |            |                   |                                             |
| Slice         | `a[0:1]` `a[0:]` `a[:-1]`         | Instance   | `A`               | `operator<A> Slice(startIndex int?, endIndex int?) { ... }`
| Indexer       | `a[0]` `a[-2]`                    | Instance   | `A`               | `operator<A> Index(index B) { ... }`
| Set Index     | `a[0] = 4`                        | Instance   | `void`            | `operator<void> SetIndex(index A, value B) { ... }`
