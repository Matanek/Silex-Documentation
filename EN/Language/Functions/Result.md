# Return a recoverable error

`Result<T,E>` represents either `success(T)` or `failure(E)`. Unlike `panic`,
the caller can inspect or propagate this failure.

```sx
enum ParseError {
    invalid(str)
}

func parse(text:str) Result<int,ParseError> {
    if text.count() == 0 {
        return Result<int,ParseError>.failure(ParseError.invalid("empty"))
    }
    return Result<int,ParseError>.success(42)
}

func main() {
    let message = match parse("42") {
        success(value) => "value: $(value)"
        failure(error) => "invalid"
    }
    print(message)
}
```

Here, `match` handles both variants without leaving an implicit failure.

## Propagate with `try`

```sx
func load(text:str) Result<int,ParseError> {
    let value = try parse(text)
    return Result<int,ParseError>.success(value)
}
```

On success, `try` produces the `T` value. On failure, it immediately returns
the same error type from the enclosing function. This is ordinary control
flow, not an exception.

For `Result<void,E>`, construct success with `success()` and write
`try operation()` as a statement.

## Handle failure locally

Add an `else` block when failure should leave the current flow differently.
Bind `error` only if the branch uses the original value:

```sx
func load(text:str) Result<int,AppError> {
    let value = try parse(text) else error {
        return Result<int,AppError>.failure(AppError.input(error))
    }
    return Result<int,AppError>.success(value)
}
```

Write `else { ... }` to deliberately ignore the error. Every path in the block
must exit with `return`, `break`, `continue`, or another guaranteed
termination; this block never supplies a replacement value.

At a command boundary, a short form replaces any error type with a `str`
failure and returns it immediately:

```sx
func load_for_cli(text:str) Result<int,str> {
    let value = try parse(text) else error "unable to parse: $(text)"
    return Result<int,str>.success(value)
}
```

The message is evaluated once on failure and never on success. The original
error is deliberately unavailable in this form.

## Transform the error type

```sx
func convert(error:ParseError) AppError {
    return AppError.input(error)
}

let config = map_error(parse(text), convert)
```

`map_error` calls the named transformation exactly once on failure and never
on success.

[Back to functions](README.md) ·
[Choose with an enum and `match`](../Data-types/Enums.md)
