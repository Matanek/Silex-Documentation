# Define static members and containers

A `static` member belongs to the type itself. Always select it with the full
type name, never with an instance.

```sx
struct Position {
    var x:int
    static let tile_width:int = 32

    static func origin() Position {
        return Position()
    }
}

func main() {
    let origin = Position.origin()
    print(origin.x)
}
```

A class accepts the same static members. They are neither inherited nor
dynamically dispatched.

A [static property](Properties.md#lazily-initialize-a-static-member) may
initialize its own storage on the first read, notably to express a singleton
without a second cache field.

## Create a container without instances

Declare `static struct` or `static class` when a type exists only to qualify
constants, shared state, or operations:

```sx
public static struct Constants {
    let canvas_width:int = 960
    let canvas_height:int = 640

    func area() int {
        return Constants.canvas_width * Constants.canvas_height
    }
}
```

All fields and methods become implicitly static. Canonical style therefore
omits `static` on each one; the redundant modifier remains accepted for source
compatibility.

A static container cannot be constructed and has no constructor, `self`,
`drop`, base class, protocol conformance, `protected` member, extension, or
type parameters of its own. Its methods may still declare their own type
parameters. Ordinary nested structures and classes remain constructible unless
their own declaration is also marked `static`.

## Initialize at compile time

Silex chooses the strategy according to the value, independently of `let` or
`var`. A statically representable value is evaluated entirely at compile time.
It may use intrinsic literals, operators, numeric conversions, immutable static
fields, functions proven to be compile-time evaluable, and similarly proven
constructors of value structures whose fields are themselves static.

```sx
struct Vec2 {
    var x:float
    var y:float

    init(x:float, y:float) {
        self.x = x
        self.y = y
    }
}

static struct Waypoints {
    let first:Vec2 = Vec2(-200.0, 200.0)
    let second:Vec2 = Vec2(200.0, 200.0)
}
```

The constructed value is stored directly in the program's static data.
Reading `Waypoints.first` produces a copy and does not call its constructor
again at runtime.

A compile-time evaluated function uses only intrinsic scalars, immutable local
variables, and other compile-time evaluable calls. This path cannot read a
`static var`, produce an effect, allocate a runtime resource, or form a
dependency cycle.

## Initialize before `main`

An owned dynamic list requires runtime storage. Silex then generates an
initializer executed before `main` or before an isolated test entry:

```sx
static struct Paths {
    let triangle:int[] = [1, 2, 3]
    var editable:int[] = [0]
}
```

`let` makes the static field immutable without requiring its value to live in
binary data. `Paths.triangle` may be read, indexed, and traversed, but neither
reassigned nor resized. `var` allows reassignment and list mutations such as
`Paths.editable.append(4)`.

Runtime static fields are initialized once, in declaration order. An
initializer may read a runtime field declared earlier; reading its own field or
a later field is rejected. Their owned storage remains alive throughout the
program or isolated test session.

The values currently accepted on this runtime path are owned dynamic lists and
value structures that contain them.

[Back to data types](README.md) ·
[Run deterministic cleanup](Cleanup.md)
