# Define a contract with a protocol

A protocol declares the methods that a conforming type must provide.

```sx
protocol Drawable {
    func draw()
}

struct Sprite:Drawable {
    func draw() {
        print("sprite")
    }
}

func main() {
    var drawable:Drawable = Sprite()
    drawable.draw()
}
```

Conformance is nominal: the type must name the protocol. A method with the
right form is not sufficient by itself.

## Require a property

A protocol may require reading, or both reading and writing, without imposing
storage:

```sx
protocol Named {
    name:str { get }
}

protocol Renamable {
    name:str { get set }
}
```

A compatible `let` or `var` field satisfies `{ get }`. Only a `var` field, or
a computed property with both getter and setter, satisfies `{ get set }`.
`let` and `var` are not allowed in the protocol itself. See the
[complete property contract](Properties.md).

## Store a conforming value

```sx
var drawable:Drawable = Sprite()
drawable.draw()
```

A protocol value exposes only the protocol requirements. A structure is copied
into this dynamic value; a class preserves its shared identity. Calls through
a protocol value are treated as mutable, so a direct receiver is declared with
`var`.

## Constrain a specialization

```sx
func render<T:Drawable>(value:T) {
    value.draw()
}

render(Sprite())
```

The compiler specializes the function for the concrete type. At this call
site, the protocol therefore creates no erased container. A type parameter
accepts at most one protocol constraint.

A class may name one base class first, then protocols. A structure names only
protocols. A derived class inherits its base's valid conformances.

[Back to data types](README.md) ·
[Add a conformance with an extension](Extensions.md)
