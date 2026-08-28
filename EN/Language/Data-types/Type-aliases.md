# Give another name to a type

An alias gives a more expressive local name to an existing type. It improves
the vocabulary of the code without creating a new type.

## Declare an alias

Place the existing name after `use`, then the local name after `as`:

```sx
use int as Count

func main() {
    let total:Count = 3
    print(total)
}
```

The program prints:

```text
3
```

Here, `Count` and `int` designate exactly the same type. A `Count` value can be
used anywhere an `int` is expected, without conversion.

## Shorten a qualified name

An alias can also shorten a name from another module:

```sx
use Geometry.Vector as Vector
```

In this file, `Vector` then designates `Geometry.Vector`. The alias neither
moves the original declaration nor creates a wrapper or additional type
identity.

Choose an alias when the new name better expresses the type's role or avoids
repeating a long path. If two values must become incompatible despite an
identical representation, an alias is insufficient: declare an actual type.

[Back to data types](README.md) ·
[Choose a built-in type](../Values/Built-in-types.md)
