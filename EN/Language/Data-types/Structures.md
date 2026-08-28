# Create a nominal value with a structure

A structure defines a value type with named fields. An ordinary assignment
copies its value.

```sx
struct Position {
    var x:int
    var y:int = 10
}

func main() {
    let origin = Position()
    var cursor = Position(x:2, y:3)
    var target = Position()
        ..x = 10
        ..y = 5

    cursor.x = 4

    print(origin.y)
    print(cursor.x)
    print(target.x)
    print(target.y)
}
```

Without a custom constructor, fields are named and may appear in any order. An
omitted field first receives its declared value, or otherwise the
[intrinsic value of its type](../Values/Default-values.md). Here, the
[cascade](../Values/Operators.md#apply-several-operations-to-the-same-object)
configures `target` immediately after its creation. The program successively
prints `10`, `4`, `10`, and `5`.

## Copy a structure

```sx
var first = Position(x:1, y:2)
var second = first

second.x = 10
print(first.x) // 1
```

Structures are nominal: two distinct declarations create two distinct types,
even with the same fields. Assignment recursively copies their fields. A class
field still refers to the same shared instance. Use
[`copy`](../Ownership/Copy-and-move.md) when the entire reachable graph must be
detached.

## Establish an invariant with `init`

```sx
struct Position {
    let x:int
    let y:int

    init(value:int) {
        self.x = value
        self.y = value
    }
}

let point = Position(5)
```

Declaring one `init` closes the automatic named-field initializer. Every `let`
field without a default value must then be initialized exactly once on every
normal constructor path.

## Add methods

```sx
struct Counter {
    var value:int

    func increment(amount:int = 1) {
        self.value += amount
    }

    func current() int {
        return self.value
    }
}
```

A method receives `self` implicitly. Silex infers whether it modifies `self`;
a modifying call requires a receiver declared with `var`.

## Nest a type

```sx
public struct Catalog {
    struct Entry {
        let value:int
    }
}

let entry = Catalog.Entry(value:42)
```

A nested type captures no container instance. A public alias or re-export of
the outer type preserves access to its nested types: an alias `Window`
therefore also exposes `Window.Settings`.

[Static members](Static-members.md), [generic structures](Generics.md), and
their [cleanup](Cleanup.md) have dedicated pages so these contracts are not
mixed with ordinary value semantics.

[Back to data types](README.md) ·
[Share an identity with a class](Classes.md)
