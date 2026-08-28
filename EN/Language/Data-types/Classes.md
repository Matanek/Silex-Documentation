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
immutable field must be initialized on every normal path before `self` can
escape.

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
