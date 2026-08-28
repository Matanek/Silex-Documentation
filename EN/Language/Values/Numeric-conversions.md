# Convert a number

The `as` operator converts a numeric value to the requested type. The
conversion is checked: Silex stops it if the target type cannot represent the
value without loss.

## Request an explicit conversion

This program converts the same integer to `uint8` and `float64`:

```sx
func main() {
    let count = 42
    let byte = count as uint8
    let precise = count as float64

    print(byte)
    print(precise)
}
```

It prints:

```text
42
42.0
```

The target type follows `as`. The compiler therefore infers `uint8` for `byte`
and `float64` for `precise`.

## Understand the check

An explicit conversion must preserve the exact value. For example,
`255 as uint8` succeeds, while `256 as uint8` fails because a `uint8` cannot
hold `256`. Likewise, a floating-point number can become an integer only when
it is in range and has no fractional part.

Some widening conversions are implicit, including `int8` to `int64`, `uint8`
to `uint64`, and `float32` to `float64`. Other family or width changes may
require `as`. You may also write it when an implicit conversion should remain
visible.

`as` converts numeric values only. It does not turn a number into text or
change the type of another value.

[Back to values](README.md) · [Choose a built-in type](Built-in-types.md)
