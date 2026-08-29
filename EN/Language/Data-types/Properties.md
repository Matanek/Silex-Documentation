# Control access with a property

A property keeps the familiar shape of a field declaration, then places its
accessors in a block. `let` exposes read-only access; `var` may also expose
write access.

```sx
struct Rectangle {
    let width:int
    let height:int

    let area:int {
        get { return self.width * self.height }
    }
}

struct Thermometer {
    var raw:int

    var celsius:int {
        get { return self.raw }
        set(value) { self.raw = value }
    }
}
```

`Rectangle(width:6, height:7).area` calls the getter. The exact assignment
`thermometer.celsius = 21` calls the setter with `21` in its explicit `value`
parameter. A setter returns no value and is not a propagation site for `try`
or `Result`: a write that may fail must remain an explicit method.

`get` and `set` are contextual. They remain available as ordinary identifiers
outside a property block.

## Separate mutability from computation

A getter cannot modify `self`. This rule makes every read valid regardless of
whether the receiver was bound with `let` or `var`: readers do not need to
know a property's implementation to determine whether `value.property` is
allowed. An instance access counter or cache must therefore use an explicit
method.

A setter may modify `self`. It consequently belongs to a `var` property; a
`let` property may declare only `get`.

A compound assignment reads and then writes the property exactly once on each
side:

```sx
thermometer.celsius += 4
```

For observable effects, this is equivalent to one getter call followed by one
setter call. The receiver value is not needlessly reevaluated.

## Modify a value obtained by reading

Only assigning the property itself calls its setter:

```sx
player.position = Position(x:10) // calls position's setter
```

A further selection operates on the getter result. When that result is a
structure, it is a temporary value and mutating it is rejected:

```sx
player.position.x = 10 // rejected when Position is a structure
```

Build and assign a new position instead. When the getter returns a class, the
result preserves its shared identity: `player.position.x = 10` modifies that
instance without calling `position`'s setter again.

## Lazily initialize a static member

A property owns hidden storage distinct from its public type. Inside its own
accessors only, its name denotes that optional storage. A static getter can
therefore perform first-time initialization without declaring a second field:

```sx
class Data {
    static let instance:Data {
        get {
            if Data.instance == null {
                Data.instance = Data()
            }
            return Data.instance
        }
    }

    private init() {}
}
```

Outside the getter, `Data.instance` has type `Data` and calls the getter.
Inside it, the same name can test and initialize the storage. Returning it
extracts it to the public type, so leaving the getter before initialization
causes a forced optional extraction failure. Static getters are serialized:
two concurrent reads cannot initialize this storage simultaneously.

## Initialization, reflection, and protocols

A computed property is not a field. It never participates in the automatic
named-field initializer. `reflect(value).fields` contains visible storage,
`reflect(value).properties` contains visible properties, and
`reflect(value).methods` contains visible methods; generated accessors are not
exposed as methods.

A protocol expresses a property contract without introducing storage:

```sx
protocol Named {
    name:str { get }
}

protocol Renamable {
    name:str { get set }
}
```

A `let` or `var` field satisfies a `{ get }` requirement with the same name
and type. Only a `var` field, or a property with both accessors, satisfies
`{ get set }`. The `let` and `var` words remain forbidden in a protocol: a
protocol describes access, never the representation that provides it.

[Back to data types](README.md) ·
[Define a contract with a protocol](Protocols.md)
