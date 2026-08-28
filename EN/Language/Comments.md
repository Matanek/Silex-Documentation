# Write a comment

Silex 0.42 recognizes `//` as a line comment. Everything after this marker is
ignored until the end of the line.

```sx
// Explain why this value is needed.
let answer = 42 // A comment may follow a statement.
```

The marker remains ordinary text inside a string:

```sx
let address = "https://silex-lang.org/"
```

Use `//` in examples and official code. `#` comments and `/* ... */` blocks
are not part of Silex 0.42.

[Back to language concepts](README.md) ·
[Find a syntax form](../Reference/Syntax.md)
