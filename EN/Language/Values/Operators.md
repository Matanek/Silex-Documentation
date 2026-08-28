# Calculate, compare, and chain operations

Silex operators preserve the type of their values and report impossible
numeric operations instead of silently producing an incorrect result.

## Calculate with numbers

```sx
func main() {
    let left = 10
    let right = 3

    print(left + right)
    print(left - right)
    print(left * right)
    print(left / right)
    print(left % right)
}
```

This program prints `13`, `7`, `30`, `3`, and `1`, each on its own line. The
`+`, `-`, `*`, and `/` operators accept numbers; `%` accepts integers only.
Silex checks overflow, division by zero, and unrepresentable negation.

Compatible integers widen within their signed or unsigned family. The presence
of a floating-point number selects the common `float32` or `float64` type.

## Compare values

Numbers recognize `==`, `!=`, `<`, `<=`, `>`, and `>=`. Strings compare their
exact UTF-8 bytes. Two structures are comparable when all their fields are;
their comparison is recursive. Enums compare their active variant and then
their associated values. Classes compare shared identity, not field contents.

## Combine conditions

```sx
if ready && count > 0 {
    work()
}

if missing || expired {
    refresh()
}
```

`&&` and `||` short-circuit evaluation: their right operand is evaluated only
when the result is not already known. `!` negates a boolean.

## Manipulate unsigned bits

`&`, `^`, `<<`, and `>>` accept unsigned integers. A shift count must fit the
width of the left operand.

```sx
let masked = flags & mask
let toggled = flags ^ mask
let shifted = value << 2
let reduced = value >> 1
```

## Change a numeric value

A mutable variable, field, or indexed element accepts `+=`, `-=`, `*=`, `/=`,
`%=`, as well as `++` and `--`. Each compound assignment performs the same
checked operation as its operator, then stores the result back in the original
place. `%=` remains integer-only.

## Apply several operations to the same object

The `..` cascade operator evaluates its receiver once, applies each segment,
and produces that same receiver after its changes:

```sx
var values:int[] = []
    ..append(10)
    ..append(20)
    ..reverse()
```

A segment is a method call or direct field assignment. Ordinary mutability,
visibility, ownership, and borrowing rules still apply. An existing value must
therefore be mutable when a segment writes to it; a new owned temporary can be
configured directly.

A single dot after a call ends the cascade and resumes ordinary access on that
call's result: `values..append(30).count()`. `..` is one token and remains
distinct from `...`, which forms an integer range.

Exact operator precedence is available in the
[syntax reference](../../Reference/Syntax.md).

[Back to values](README.md) ·
[Choose a branch or loop](../Control-flow/Conditions-and-loops.md)
