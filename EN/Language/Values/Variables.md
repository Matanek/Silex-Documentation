# Store and change values

A program gives names to the values it uses. Some will not change; others must
evolve.

## Choose between `let` and `var`

This program keeps the language name and updates a lesson counter:

```sx
func main() {
    let language:str = "Silex"
    var lessons = 1

    lessons = 2

    print(language)
    print(lessons)
}
```

`language` keeps the same value. `lessons` starts at `1`, then receives `2`.

Use `let` when the name will keep the value received at its declaration. The
compiler rejects assigning another value to that name.

Use `var` when the program must assign a different value. Here,
`lessons = 2` therefore requires `lessons` to be declared with `var`.

Start with `let` and change to `var` only when mutation is needed. This keeps
the code's intention visible from the declaration.

## State or infer the type

An annotation after the name fixes the type explicitly, as in
`let language:str = "Silex"`.

Without an annotation, the compiler infers the type from the first value. In
the example, it infers that `lessons` is an `int` from `1`.

To compare available types and choose a precise width, see
[Silex built-in types](Built-in-types.md).

## Change a field

The root and every traversed field must be mutable:

```sx
struct Position {
    var x:int
    var y:int
}

func main() {
    var position = Position(x:1, y:2)
    position.x = 10
    print(position.x)
}
```

A field declared with `let`, or a `let` encountered before it in the path,
makes the rest of the path read-only.

## Mark shared state

A binding that can reach a class instance is declared with `var` when that
instance may change:

```sx
var player = Player()
player.damage(10)
```

Even if `player` still designates the same instance, `var` indicates that the
shared state it reaches may be mutated. Instance behavior is detailed with
[classes](../Data-types/Classes.md).

## Respect lexical scope

A name exists only in the block where it is declared and its child blocks. A
visible parameter or local variable cannot be redeclared with the same name.
Two sibling branches may each use the same local name because their scopes do
not overlap.

[Back to values](README.md) · [Choose a built-in type](Built-in-types.md)
