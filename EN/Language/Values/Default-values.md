# Omit an initial value

A declaration that states its type can sometimes omit the value after `=`.
Silex then initializes the variable with that type's intrinsic value. The
variable therefore always receives a deterministic value.

## Use the intrinsic value

This program lets Silex initialize three common values:

```sx
func main() {
    let count:int
    let ratio:float
    let ready:bool

    print(count)
    print(ratio)
    print(ready)
}
```

It prints:

```text
0
0.0
false
```

The annotation is required when the initial value is absent: without it, the
compiler cannot determine which type to create.

## Find common intrinsic values

| Type | Intrinsic value |
| --- | --- |
| Signed or unsigned integer | `0` |
| `float32`, `float64`, or `float` | `0.0` |
| `bool` | `false` |
| `str` | empty string `""` |
| Optional type `T?` | `null` |

## Initialize a class

A class has no intrinsic instance. A class variable must receive an instance,
as in `var player = Player()`. If the absence of an instance is valid, declare
an optional instead with `var selected:Player?`; its initial value is `null`.

Inside a constructor, this rule does not force a field without an intrinsic
value to become optional. The field may remain pending and receive its first
value through `self.field = value`, provided every normal path initializes it
before any read or use of `self`.

[Back to values](README.md) · [Choose a built-in type](Built-in-types.md)
