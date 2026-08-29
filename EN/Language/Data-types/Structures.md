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

The declared value after `=` may be an ordinary expression, including a
function call or cascade. Silex evaluates it in field order for each new
value: a list or another resource created this way is not accidentally shared
between two constructions. This expression has access to neither `self` nor a
constructor's parameters.

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
field without a declared value must then be initialized exactly once on every
normal constructor path. The same applies to a `var` field whose type provides
neither an intrinsic value nor construction without arguments. Initialized
fields may be read to compute later fields, but `self` as a whole cannot be
used until initialization is complete.

A field expression suits values independent from constructor arguments,
`init` suits invariants that depend on them, and the automatic named-field
initializer suits values callers should choose. An optional type remains
reserved for a valid absence in the model, not temporary storage for a field
under construction.

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

A member that computes or controls its access is declared as a
[property](Properties.md). It does not thereby become a parameter of the
automatic named-field initializer.

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
