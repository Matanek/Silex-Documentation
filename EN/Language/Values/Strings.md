# Build text

A `str` value contains immutable UTF-8 text. You can combine strings with `+`
or inject an expression directly with `$(...)`.

## Concatenate and interpolate

```sx
func main() {
    let name = "Ada"
    let answer = 42
    let greeting = "Hello, " + name

    print(greeting)
    print("The answer is $(answer)")
}
```

The program prints:

```text
Hello, Ada
The answer is 42
```

Only `$(` starts interpolation. The parenthesized expression is evaluated
once. Write `$$` to obtain a literal dollar sign: `"$$(answer)"` produces the
text `$(answer)`.

## Escape a character

Strings recognize `\\`, `\"`, `\n`, `\r`, `\t`, `\0`, and `\u{H...}`, among
other forms. The last form writes a Unicode scalar from its hexadecimal value:

```sx
let quoted = "\"Silex\""
let smile = "\u{1F642}"
```

## Write a string across several lines

A quote immediately followed by a line break starts a block string:

```sx
let paragraph = "
    First line.
    Second line.
    "
```

Its value is `First line.\nSecond line.`. Silex removes the structural opening
and closing line breaks, then removes from every non-empty line the indentation
before the closing quote. Additional indentation remains in the value. All
source-file line endings are normalized to `\n`.

The closing quote must begin its line after optional indentation. A quote
elsewhere on a line is written `\"`. Block strings accept the same escapes and
interpolations as ordinary strings.

## Count and compare text

`count()` returns the number of Unicode scalars, not the number of UTF-8 bytes.
The `==` and `!=` operators compare exact UTF-8 bytes instead; they perform no
Unicode normalization.

[Back to values](README.md) · [Calculate and compare](Operators.md)
