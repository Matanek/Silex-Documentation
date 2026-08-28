# Add methods with an extension

An extension adds methods to an existing structure or class without modifying
its fields or identity.

```sx
struct Counter {
    var value:int
}

extend Counter {
    func increment(amount:int = 1) {
        self.value += amount
    }

    static func zero() Counter {
        return Counter(value:0)
    }
}

func main() {
    var counter = Counter.zero()
    counter.increment(2)
    print(counter.value)
}
```

The program prints `2`. Fields come from the original type; the extension adds
only `increment` and `zero`.

An extension cannot add a field, constructor, `drop`, override, or `protected`
member.

Importing an extension's module activates its public methods. Placing the file
next to the target type is not enough. A qualified reference can also load a
child module on demand: after `use STD.Math`, using `Math.Vec3` loads that
child and activates the extensions it declares. Unused children are never
loaded eagerly merely because their parent namespace is available.

## Add a conformance

```sx
extend Sprite:Drawable {
    func draw() {
        print("sprite")
    }
}
```

The conformance is visible in files that activate the extension provider with
`use`, directly or transitively.

An extension targets an existing non-generic structure or class. An extension
method on that type may still declare its own type parameters.

[Back to data types](README.md) ·
[Organize imports](../Modules/Imports.md)
