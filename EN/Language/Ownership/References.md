# Borrow a value

Reference modes belong to parameters and return types. Call syntax remains
ordinary.

## Borrow for reading

```sx
struct Box {
    let value:int
    func get() int { return self.value }
}

func inspect(box:@Box) int {
    return box.get()
}

func main() {
    let box = Box(value:42)
    print(inspect(box))
}
```

`@T` expresses read access to the borrowed path. The function may read it or
pass it to another `@T`, but cannot modify it, transfer it, store it, or return
that capability directly.

It may return an ordinary copy when `T` is a value that cannot let a reached
class reference escape:

```sx
func copied(value:@int) int { return value }
```

For a class, fields and methods reached through the borrowed path remain
read-only. An independent alias may still modify the same instance: `@` does
not globally freeze the object.

## Borrow for modification

```sx
func increment(value:&int) {
    value += 1
}

var count = 1
increment(count)
print(count) // 2
```

`&T` designates the caller's mutable location. Writes are visible in the
provided `var`, field, or indexed element.

For a class, an ordinary `Class` parameter may modify the shared instance but
cannot replace the caller's reference. `&Class` can do both.

Like `@T`, `&T` may produce an ordinary copy when no borrowed capability can
escape. Explicitly returning `@T` or `&T` remains the way to return an alias
instead of a copy.

## Return a borrow

The return expression does not repeat `@` or `&`:

```sx
func inspect(owner:@Owner) @State {
    return owner.state
}

func edit(owner:&Owner) &State {
    return owner.state
}
```

With one compatible parameter, provenance is implicit. If several parameters
could be the source, name it in the return type:

```sx
func choose(first:@State, second:@State) @first:State {
    return first
}
```

A borrowed result may live in a lexical local variable, but it cannot outlive
its root or be stored in an aggregate.

`T`, `@T`, and `&T` do not form distinct overload signatures.

[Back to ownership](README.md) ·
[Borrow a collection view](../Collections/Views.md)
