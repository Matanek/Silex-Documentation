# Select a value with `match`

An enum declares a closed set of variants. A variant may carry associated
values.

```sx
enum Connection {
    waiting
    connected(str)
    closed(str)
}

func main() {
    let pending = Connection.waiting
    let active = Connection.connected("server")
    print(pending != active)
}
```

A variant without an associated value is already a value and needs no
parentheses. The older `Connection.waiting()` form remains accepted. A variant
with values remains a construction and requires its parentheses.

The word `in` is contextual in enum declarations, variant accesses, and
`match` branches. An intentional name such as `Easing.in` therefore remains
available.

## Produce a value with `match`

```sx
func describe(connection:Connection) str {
    return match connection {
        waiting => "waiting"
        connected(name) => name
        closed(reason) => reason
    }
}
```

The subject is evaluated once and is not consumed. Every variant appears
exactly once, and every branch produces exactly the same type.

Write `_` to deliberately ignore an associated value. It still occupies its
position but declares no variable; the value remains owned by the enum and
follows its ordinary lifetime. `let _` and `var _` are invalid. Only an `else`
branch absorbs all remaining variants.

## Filter a variant with a guard

```sx
let category = match token {
    integer(value, _) if value < 0 => "negative"
    integer(value, _) if value == 0 => "zero"
    integer(_, _) => "positive"
    identifier(name, _) if name == "self" => "reserved"
    identifier(_, _) => "name"
    else => "other"
}
```

The guard runs only after its variant matches, sees the pattern bindings, and
must produce a `bool`. A false guard continues with the next branch.

A guarded branch never proves exhaustiveness: every variant eventually needs
an unguarded branch unless `else` covers the remaining cases. An unguarded
branch makes any later branch for the same variant unreachable.

## Execute statements in branches

```sx
match connection {
    waiting => { print("waiting") }
    connected(name) => { print(name) }
    closed(reason) => { print(reason) }
}
```

Block branches and expression branches cannot be mixed. A branch may contain
another `match`, including a `match` expression whose result becomes the value
of the enclosing branch. Every level retains its own bindings and
exhaustiveness rules.

## Select a scalar literal

`match` also accepts a `bool`, integer, or `str` subject. Every pattern is a
literal with the same type as the subject; an integer literal adopts the
subject's integer type and must fit its range.

```sx
func status(code:int) str {
    return match code {
        200 => "ok"
        404 => "not found"
        else => "other"
    }
}
```

Branches are tried in order, and the first matching branch whose guard
succeeds is selected. The subject is evaluated once. An unguarded branch makes
any later identical pattern unreachable; two unguarded branches cannot repeat
the same literal.

An integer or `str` match requires an `else` branch. A boolean match may omit
it when `true` and `false` each have an unguarded branch. Branches never fall
through implicitly: use a block in each branch to execute statements.

## Associate a raw value

```sx
enum Direction:int {
    north = 1
    south = -2
}

let code:int = Direction.north.raw_value
```

A raw enum uses `int` or `str`. Every variant supplies a unique literal. The
enum and its raw type never convert implicitly in either direction.

## Compare and copy

`==` first compares the active variant, then recursively compares all its
associated values. Different variants are unequal; `!=` reverses this result.
Associated values must themselves be comparable: structures compare their
fields and classes their shared identity. A raw enum always compares its
variant, never its `raw_value` implicitly.

Copying an enum compositionally copies the contents of its active variant. If
that content has a `drop`, every copy owns its own content and destroys only
the active variant.

Generic enums are covered with [generic types](Generics.md).

[Back to data types](README.md) ·
[Return a recoverable error](../Functions/Result.md)
