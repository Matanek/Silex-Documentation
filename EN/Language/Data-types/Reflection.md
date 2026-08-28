# Inspect a value's metadata

The compiler-provided `reflect` function returns source-level metadata without
modifying or consuming its value.

```sx
func main() {
    let value = 42
    let metadata = reflect(value)
    print(metadata.type)
}
```

`type` contains the canonical spelling of the static type. `name` contains the
canonical source declaration represented by the expression: nominal type,
enum variant, selected member, or named function. A computed unnamed scalar
exposes only `type`; requesting its `name` produces a compilation diagnostic.

For an enum, `name` follows its active variant. Names from packages and named
modules contain their complete canonical path, such as
`GFX.Animation.Easing.constant`. The internal entry-module name, often `Main`,
is omitted. A local alias never changes this identity.

## Inspect a selected member

```sx
struct Foo { let name:str = "Foo name" }

assert(reflect(Foo().name).name == "Foo.name")
assert(reflect(Foo().name).type == "str")
```

The name preserves the declaration path; `type` describes the selected value.

## Inspect enum variants

`variants` provides every variant name in source order. A variant with content
contributes only its name:

```sx
enum Message {
    empty
    text(str)
}

let metadata = reflect(Message.empty)
assert(metadata.variants[0] == "empty")
assert(metadata.variants[1] == "text")
```

## Inspect a structure or class

`fields` and `methods` contain the instance fields and methods visible at the
call site, in declaration order. The `private`, `protected`, `package`,
`module`, and `local` scopes still apply: reflection reveals nothing that
ordinary code could not already reach at the same location. Static members are
not part of instance reflection.

## Inspect a function

```sx
func predicate(value:int) bool { return value > 0 }
let metadata = reflect(predicate)

assert(metadata.type == "func(int)bool")
assert(metadata.name == "predicate")
assert(metadata.parameters[0] == "int")
assert(metadata.return_type == "bool")
```

Borrowed modes remain present in the type spelling. `reflect` evaluates its
argument exactly once and does not transfer it. The compiler emits only the
ordinary strings and lists requested by the category; no memory address, field
offset, machine symbol, or stable ABI is exposed.

[Back to data types](README.md) ·
[Understand intrinsic classes](Intrinsic-classes.md)
