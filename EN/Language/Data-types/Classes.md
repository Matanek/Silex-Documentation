# Share an identity with a class

A class defines shared objects with automatic lifetimes. Passing, copying, or
storing a class value preserves the identity of the same instance.

```sx
public class Player {
    private let name:str
    var health:int = 100

    init(name:str) {
        self.name = name
    }

    func damage(amount:int) {
        self.health -= amount
    }

    func description() str {
        return "$(self.name): $(self.health)"
    }
}

func main() {
    var player = Player("Ada")
    player.damage(10)
    print(player.description())
}
```

Use `var` for a binding that allows access to a class's mutable state, even
when that binding never changes instance.

## Initialize a field at its declaration

The value after `=` may be an ordinary expression: a function call,
construction, calculation, or cascade. Silex evaluates it in field order for
each new instance, before the constructor body. Two instances therefore do not
accidentally share a collection created by the same expression.

```sx
func initial_values() int[] { return [1] }

class Recorder {
    var values:int[] = initial_values()
}

func main() {
    var first = Recorder()
    var second = Recorder()
    first.values.append(2)

    print(first.values.count())  // 2
    print(second.values.count()) // 1
}
```

Use this form when the value depends on neither `self` nor a constructor
argument: those names are not available in the expression. It can also prepare
an object through a cascade without requiring a temporary optional.

## Construct an instance

Without a custom constructor, initialize visible fields by name:

```sx
class Position {
    var x:int
    var y:int
}

func main() {
    var position = Position(x:2, y:3)
    var target = Position()
        ..x = position.x
        ..y = position.y

    position.x = 4

    print(position.x)
    print(target.x)
    print(target.y)
}
```

Here, the
[cascade](../Values/Operators.md#apply-several-operations-to-the-same-object)
configures a new instance from `position`. The program prints `4`, `2`, then
`3`: modifying `position` afterward does not modify `target`, because every
call to `Position()` creates a distinct identity.

Declare `init` when construction must establish an invariant. As soon as one
constructor exists, the automatic named-field initializer disappears. Every
`let` field without a declared value and every `var` field whose type provides
neither an intrinsic value nor construction without arguments then remains
pending. The constructor must assign them on every normal path. An initialized
field may be read; `self` as a whole cannot be used until every field is
initialized.

```sx
struct Range {
    let minimum:float
    let maximum:float

    init(minimum:float, maximum:float) {
        self.minimum = minimum
        self.maximum = maximum
    }
}

class Gauge {
    var limits:Range

    init(minimum:float, maximum:float) {
        self.limits = Range(minimum, maximum)
    }
}
```

Inside `init`, an assignment such as `self.field = value` initializes the
target field; it is not premature use of the complete object. A field that is
already initialized may then be read or mutated while other fields remain
pending. Loops may therefore prepare storage whose size depends on an
argument:

```sx
class PreparedList {
    var values:int[]
    let requested_count:int

    init(count:int) {
        self.values = []
        var index = 0
        while index < count {
            self.values.append(index)
            index++
        }
        self.requested_count = count
    }
}
```

Reading a pending field or passing `self` as a complete object remains
invalid. An assignment that occurs only inside a loop which may never execute
also cannot establish initialization on every path; the field must receive a
value on the loop's exit path.

The three forms complement one another: place an expression on the field for
a value owned by each instance, use `init` when the value depends on its
arguments, and retain the automatic named-field initializer when callers
should freely provide the configuration. Declare `T?` only when absence is a
real part of the model; it is not an intermediate state required by
construction.

## Add methods

Methods receive `self` implicitly and accept positional arguments, named
arguments, and default values like ordinary functions. Silex infers whether a
method modifies the instance; such a call requires `var` access to the shared
identity.

Fields, constructors, and methods inherit class visibility. A modifier can
restrict it without ever exceeding the type's visibility. Exact scopes are
grouped in [declaration visibility](../Modules/Visibility.md).

## Compare identities

`==` compares the shared identity of two class values, not their fields.
Duplication and borrowing rules are detailed in
[copy or transfer a value](../Ownership/Copy-and-move.md) and
[borrow a value](../Ownership/References.md).

Source code never manipulates an object's address, reference count, allocation,
or manual release. Inheritance, static members, generic specializations, and
cleanup each have a dedicated contract.

[Back to data types](README.md) ·
[Specialize a class through inheritance](Inheritance.md)
