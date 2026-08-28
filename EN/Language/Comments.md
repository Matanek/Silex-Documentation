# Write a comment

Silex accepts two line-comment forms and one block-comment form. Use `//` in
examples and official code to keep one consistent style.

## Comment to the end of the line

`//` and `#` ignore everything that follows until the line break. Either form
may start a line or follow a statement.

```sx
// Explain why this value is needed.
let answer = 42 # This form is accepted too.
```

## Comment out a region

`/*` opens a block comment and `*/` closes it. The block may cover part of a
line or several lines. It may also contain other blocks:

```sx
func main() {
    /* Temporarily disable this region.
        /* A nested block remains properly delimited. */
    */
    let answer = 40 /* This part is ignored. */ + 2
    print(answer)
}
```

An unclosed block produces a diagnostic at its opening `/*`. A line break
inside a block keeps its statement-termination role, so keep a comment inserted
in the middle of an expression on the same line.

Markers inside a string remain ordinary text:

```sx
let text = "# // /* this is not a comment */"
```

[Back to language concepts](README.md) ·
[Find a syntax form](../Reference/Syntax.md)
