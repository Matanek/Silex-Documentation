# Adapt a function to several types

A type parameter lets you write a behavior once, then specialize it with the
type actually used.

```sx
func identity<T>(value:T) T {
    return value
}

func main() {
    let inferred = identity(42)
    let explicit = identity<str>("Silex")
    print(inferred)
    print(explicit)
}
```

Provide all type arguments or let Silex infer all of them from ordinary
arguments. The expected return type does not participate in this inference.

A type argument may itself be a generic specialization, including when a named
callback uses the same concrete type:

```sx
func passing(entry:@Entry<str, int>) bool {
    return entry.value >= 10
}

let explicit = count_where<Entry<str, int>>(entries, passing)
let inferred = count_where(entries, passing)
```

## Require a protocol

Add a protocol after `:` to restrict accepted types:

```sx
func render<T:Drawable>(value:T) {
    value.draw()
}
```

Specialization checks that the concrete type nominally conforms to the
required protocol. This constraint is static; it adds no dynamic dispatch at
runtime.

[Back to functions](README.md) ·
[Define a protocol](../Data-types/Protocols.md)
