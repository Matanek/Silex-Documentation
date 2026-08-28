# Borrow a collection view

A view shares a storage slice without owning its elements. `@` creates a
read-only view and `&` a mutable view.

```sx
func main() {
    var values = [10, 20, 30, 40, 50]
    {
        let middle = @values[1:4]
        print(middle[0])
    }

    {
        var editable = &values[1:4]
        editable[0] = 42
    }
    print(values[1])
}
```

Both bounds are required. The start is included and the end excluded. Negative
bounds are made relative to `count()`, then both bounds are clamped to the
collection limits.

## Accept a view

```sx
func sum(values:@int[..]) int {
    var total = 0
    for value in values {
        total += value
    }
    return total
}
```

## Return a view

```sx
func identity(values:@int[..]) @values:int[..] {
    return values
}
```

A view may live in a lexical binding or cross borrowed parameters and returns
whose provenance is declared. It owns neither storage nor elements.

Shared and mutable views accept `count`, `is_empty`, indexing, subviews, and
iteration. A mutable view also accepts indexed writes, `for var`, and `swap`.
No view can resize, globally reorder, remove, or transfer elements.

A view cannot be stored in an optional, structure, enum, collection, static
field, or capture. A slice without `@` or `&` remains an independent copied
list.

[Back to collections](README.md) ·
[Understand references](../Ownership/References.md)
