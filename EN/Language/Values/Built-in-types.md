# Choose a built-in type

Silex provides built-in types for numbers, logical values, and text. The
compiler can often infer them from the initial value; an explicit annotation
remains useful when a precise width is required.

## Let Silex infer the type

In this program, the first four declarations are inferred. Only `channel`
requires a type:

```sx
func main() {
    let count = 42
    let ratio = 0.5
    let ready = true
    let title = "Silex"
    let channel:uint8 = 255

    print(count)
    print(ratio)
    print(ready)
    print(title)
    print(channel)
}
```

The compiler chooses `int`, `float`, `bool`, and `str` for `count`, `ratio`,
`ready`, and `title`, respectively.

The program prints:

```text
42
0.5
true
Silex
255
```

## Know the available types

| Value | Available types | Common name |
| --- | --- | --- |
| Signed integer | `int8`, `int16`, `int32`, `int64`, `int` | `int` |
| Unsigned integer | `uint8`, `uint16`, `uint32`, `uint64`, `uint` | `uint` |
| Floating-point number | `float32`, `float64`, `float` | `float` |
| Logical value | `bool` | `bool` |
| Text | `str` | `str` |

The common names map to precise widths: `int` is `int64`, `uint` is `uint64`,
and `float` is `float32`.

## Choose an explicit width

Use the common names for ordinary calculations. Choose an explicit width when
a value must fit a specific range, such as `uint8` for a component between `0`
and `255`, or `float64` when a calculation needs more precision.

A type annotation follows the name directly, with no spaces around `:`:
`channel:uint8`.

If a typed declaration has no value after `=`, see
[the initial values chosen by Silex](Default-values.md).

To move explicitly between numeric types, see
[numeric conversions](Numeric-conversions.md). To use a clearer name without
creating a new type, see [type aliases](../Data-types/Type-aliases.md).

[Back to values](README.md) · [Understand variables](Variables.md)
