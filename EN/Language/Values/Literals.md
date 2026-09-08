# Write a literal value

A literal writes a value directly in source code. Silex 0.44 recognizes the
following forms.

## Numbers

```sx
let decimal = 1_000
let binary = 0b1010
let octal = 0o12
let hexadecimal = 0xFF
let exponent = 1.5e2
```

An underscore may separate digits. The `0b`, `0o`, and `0x` prefixes select
bases 2, 8, and 16, respectively. A number with a point or exponent is a
floating-point number.

## Booleans, text, and absence

```sx
let enabled = true
let disabled = false
let line = "first line\nsecond line"
let scalar = "\u{1F642}"
let missing:Item? = null
```

Ordinary strings, escapes, block strings, and interpolation are detailed in
[the text documentation](Strings.md). `null` has an optional type determined
by its context.

[Back to values](README.md) · [Choose a built-in type](Built-in-types.md)
