# Specialize a class through inheritance

A class may extend at most one base class. The base is constructed before the
derived class.

```sx
class Entity {
    let position:int

    init(position:int) {
        self.position = position
    }
}

class Player:Entity {
    let name:str

    init(name:str, position:int):super(position) {
        self.name = name
    }
}

func main() {
    var entity:Entity = Player("Ada", 42)
    print(entity.position)
}
```

Omitting `:super(...)` is equivalent to calling `:super()`.

## Override a method

```sx
class Entity {
    func update() {
        print("entity")
    }
}

class Player:Entity {
    override func update() {
        super.update()
        print("player")
    }
}
```

Any inherited non-private instance method may be overridden. Overload
resolution uses the receiver's visible type; the selected method is then
dispatched according to the instance's actual class.

Constructors, private methods, static methods, and methods added by an
extension are not virtual.

## Preserve the mutation contract

The ability to modify the receiver is inferred across the entire virtual
family. If an override body modifies `self`, the base method and all its
overrides share a mutation-capable contract. This rule notably allows a base
class to declare an empty hook whose specialized implementation alone changes
state.

A call selected through the base type follows that shared contract: it needs a
mutation-capable receiver and remains forbidden through a read reference `@`.
Conversely, a nonmutating body in another override does not narrow the family
contract, because dynamic dispatch may reach an implementation that modifies
`self`.

A derived class also inherits its base's valid protocol conformances.
[Cleanup](Cleanup.md) follows the dynamic class toward its bases.

[Back to data types](README.md) ·
[Define a protocol](Protocols.md)
