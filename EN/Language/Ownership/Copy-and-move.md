# Copy or transfer a value

An ordinary assignment compositionally copies structures, enums, optionals,
arrays, and lists. Any class references they contain remain shared.

```sx
struct Position {
    var x:int
    var y:int
}

func main() {
    var first = Position(x:1, y:2)
    var second = first
    second.x = 10

    print(first.x)
    print(second.x)
}
```

After the following copy, both structures still reach the same `State`
instance:

```sx
var foo1 = Foo(value:10, instance:State(value:5))
var foo2 = foo1

foo2.instance.value = 8
print(foo1.instance.value) // 8
```

## Detach the entire reachable graph

```sx
var foo3 = copy foo1

foo3.instance.value = 12
print(foo1.instance.value) // 8
```

`copy` recursively recreates reached class instances. Repeated references
remain repeated in the clone, and cycles remain cycles. No constructor runs
again.

A class declared `nocopy` represents a native or external identity that
has no safe duplication operation: a device, buffer, system file, or similar
resource. Ordinary assignment still shares that identity, directly or through
a structure. However, `copy` is rejected statically as soon as the value can
reach such a class: it never silently duplicates a handle and its cleanup.

```sx
nocopy class DeviceHandle {}
struct Owner { var handle:DeviceHandle }

var handle = DeviceHandle()
let owner = Owner(handle:handle)
let shared = owner       // shares the same identity
// let detached = copy owner // error: reaches a non-clonable class
```

The compiler captures one consistent logical snapshot of the graph. A
concurrent Silex mutation is ordered before or after that snapshot; the
detached copy cannot mix two states of the source.

## Read a field from a temporary value

In `make_label().text`, the selected field remains valid after the temporary
value returned by `make_label` is released. Its other owned fields are released.
An extracted class reference keeps its shared identity; it is not cloned.
This also applies to named tuples and the present branch of optional access.

Constructing a tuple from existing values or destructuring it into bindings
preserves the same copy and transfer rules as other values.

## Transfer ownership

```sx
var original = Position(x:1, y:2)
let transferred = move original

original = Position()
```

`move` consumes a complete local variable or an ordinary parameter. The source
cannot be read again until a consumed `var` receives a complete replacement
value. A consumed `let` cannot be initialized again.

The consumed source no longer runs `drop`; the transferred value owns the
remaining cleanup. Fields, indexed elements, `self`, and temporary expressions
are not accepted sources for an explicit `move`.

[Back to ownership](README.md) ·
[Borrow without transferring](References.md)
