# Print a result or stop the program

Silex provides three direct effects for observing or stopping a program:
`print`, `assert`, and `panic`.

## Print values

```sx
func main() {
    let x = 10
    let y = 5
    print("x=", x, ", y=", y)
}
```

`print` evaluates its arguments from left to right, inserts no separator, and
adds one final line break. It accepts strings, numbers, and booleans. The
program above prints:

```text
x=10, y=5
```

[String interpolation](Values/Strings.md) can prepare the same text before the
call when the result must be reused.

## Check a condition

```sx
assert(count > 0, "count must be positive")
```

If the condition is false, `assert` writes a source-located diagnostic to
standard error, then exits the program with status `1`. A true condition
produces nothing and lets execution continue.

## Stop with a message

```sx
panic("impossible state")
```

`panic` always writes a source-located diagnostic to standard error and exits
with status `1`. Prefer [a `Result`](Functions/Result.md) when the caller must
be able to recover from the failure.

[Back to language concepts](README.md)
