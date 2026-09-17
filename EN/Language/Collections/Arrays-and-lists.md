# Choose an array or a list

A fixed array encodes its size in its type. A dynamic list can be resized.

## Use a fixed array

```sx
func main() {
    var axes:int[3] = [10, 20, 30]
    let empty:int[0] = []

    axes[-1] = 40
    print(axes.count())
    print(empty.is_empty())
}
```

A negative index counts from the end. An out-of-bounds index terminates the
program with a bounds diagnostic.

## Use a dynamic list

```sx
var scores:int[] = []
let inferred = [10, 20, 30]

scores = inferred
scores[-1] = 40
```

An empty literal requires an expected type. A non-empty literal infers its
element type from the first value.

## Modify a collection

Arrays and lists accept `swap`, `reverse`, and `replace`. Lists also accept
`append`, `prepend`, `insert`, `take`, `take_first`, `take_last`, and `clear`.
Every modifying operation requires a `var` receiver.

```sx
values.swap(0, 2)
values.reverse()
let previous = values.replace(1, 42)

items.append(item)
items.prepend(item)
items.insert(1, item)
let removed = items.take(0)
items.clear()
```

A collection can be reached through several stored fields. The starting
binding and every traversed field must be mutable:

```sx
class Scores { var values:int[] = [] }
class Player { var scores:Scores = Scores() }

func main() {
    var player = Player()
    player.scores.values.append(42)
    assert(player.scores.values.count() == 1)
    player.scores.values.clear()
    assert(player.scores.values.is_empty())
}
```

The receiver is selected before the arguments. If an argument replaces the
object that owns the collection, the operation finishes on the original
object; its destruction waits until that operation is complete.

Fields of a stored structure can be modified directly through an index. Silex
rebuilds the value path inside the collection while preserving its value
semantics:

```sx
vertices[index].position = vertices[index].position.add(offset)
vertices[index].color.a = 0.5
```

Every traversed field must be mutable, and the collection must be reached
through a mutable binding or reference.

## Copy a slice

```sx
let middle = values[1:4]
```

Both bounds are required. The start is included, the end excluded, and
negative bounds are relative to `count()`. The result is an independent list.
Use a [view](Views.md) when the slice should borrow the original storage.

When elements declare `drop`, each collection copy owns its own values.
`clear()` destroys them from the last index to the first.

[Back to collections](README.md) ·
[Traverse values](Iteration.md)
