# Group values with a tuple

A named tuple suits a small result whose roles are stable but do not justify a
nominal structure.

```sx
func size() (width:int, height:int) {
    return (width:1280, height:720)
}

func main() {
    let value = size()
    print(value.width)
    print(value.height)
}
```

Declared names belong to the tuple type. Construction repeats all of them in
declaration order, preventing two values of the same type from being silently
reversed. The editor can suggest these named members.

## Use a positional tuple

```sx
func bounds() (int, int) {
    return (0, 100)
}

let (minimum, maximum) = bounds()
```

A named tuple can be destructured in the same order. Destructuring binds
exactly one name per element. Positional tuples expose no named members.

## Compose a tuple type

A tuple contains at least two elements, may mix types, and may be nested,
stored, passed, returned, or used as a collection element:

```sx
let samples:(int, bool)[2] = [(1, true), (2, false)]
let nested:((x:int, y:int), bool) = ((x:10, y:20), true)
```

A single expression in parentheses remains simple grouping, never a
one-element tuple. Tuple elements are immutable. Copy, transfer, lifetime, and
destruction apply recursively to their values.

A function always returns one value: that value may be a tuple, but a tuple
does not introduce a separate multiple-return mechanism. Its memory layout,
alignment, and return convention remain compiler details and are unavailable
in C declarations.

## Describe a borrowed access pattern

`@` and `&` can annotate tuple elements when a generic API explicitly consumes
it as a non-storable pattern. For example, GFX uses:

```sx
ECS.Query<(@Velocity, &Transform)>
```

This pattern is not a tuple value: it cannot be independently constructed,
stored, returned, or destructured. The consuming API creates lexical borrows
on every iteration; annotations remain read and modification intentions, never
storable references.

[Back to data types](README.md) ·
[Borrow a value](../Ownership/References.md)
