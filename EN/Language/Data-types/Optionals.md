# Represent an absent value

Add `?` to a value type to represent either `null` or a value of that type.

```sx
struct Position {
    var x:int
}

func main() {
    let count:int?
    let title:str? = "Silex"
    var position:Position? = null

    print(count == null)
    print(title!)
    print(position == null)
}
```

A `T` value is automatically promoted to `T?`, but extraction is never
implicit. The `null` literal requires an expected optional type.

## Compose suffixes

Type suffixes compose from left to right. `Position?[]` is a list of optional
positions; `Position[]?` is a list that is itself optional. `int?[3]` is a
fixed array of three optional integers.

Optional suffixes may repeat. `T??` means `(T?)?`: `null` initializes an absent
outer layer, promoting a `T?` adds a present outer layer, and promoting a `T`
makes every layer present. A conditional binding and the `!` suffix remove one
layer at a time; `value!!` is therefore explicit when two layers must be
forced.

## Prove the presence of a local variable

```sx
if position != null {
    print(position.x)
}

if position == null {
    print("absent")
} else {
    print(position.x)
}
```

The proof belongs to that variable and branch. Reassigning a `var` invalidates
the proof for subsequent statements.

## Bind the present value directly

```sx
if position = find_position() {
    print(position.x)
}

while var item = next_item() {
    item.advance()
}
```

Without a modifier, the local binding is immutable; explicit `let` has the
same behavior, and `var` creates a mutable local copy. The source is evaluated
once on every attempt.

## Access without forcing presence

```sx
let x:int? = profile?.position?.x
position?.translate(3)
```

Every optional step requires its own `?.`. Arguments to a safe method are
evaluated only when the receiver is present. A safe call that modifies the
value requires an optional `var` location.

An assignment can also traverse an optional path:

```sx
profile?.position?.x = 10
statistics?.accepted += 1
state?.values[index] = value
```

The root must be `var`. Silex evaluates the path from left to right and stops
without effect at the first absent receiver. Indices and the right-hand value
are evaluated only after every preceding segment is present. Simple
assignment, compound assignment, `++`, and `--` share this short circuit. The
statement produces no value.

## Force a present value

```sx
let configuration = load_configuration()!
print(configuration.name)
```

The `!` suffix evaluates its operand once and removes exactly one optional
layer. Absence stops execution with the localized error
`forced optional extraction failed`. The `!value` prefix remains Boolean
negation; `value!` is the optional assertion.

## Choose a replacement value

```sx
let display_name = declaration.alias ?? declaration.name
let port = configured_port() ?? default_port()
```

`??` evaluates its optional left operand once. A present value is returned
without evaluating the right side; absence evaluates the replacement. A `T`
replacement produces a `T`, while `T? ?? T?` remains optional. The operator is
right-associative and has lower precedence than logical and arithmetic
operators.

[Back to data types](README.md) ·
[Choose with an enum and `match`](Enums.md)
