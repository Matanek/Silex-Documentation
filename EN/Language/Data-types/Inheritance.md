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

The ability to modify the receiver belongs to the inherited method contract.
An override cannot start modifying `self` if the base method is read-only.
Conversely, overriding a mutable method preserves that contract even if the new
body does not itself write to `self`; dynamic calls continue to preserve the
receiver state expected by the base slot.

A derived class also inherits its base's valid protocol conformances.
[Cleanup](Cleanup.md) follows the dynamic class toward its bases.

[Back to data types](README.md) ·
[Define a protocol](Protocols.md)
