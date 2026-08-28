# Adapt a type to several concrete types

A generic structure, class, or enum describes a family of types. A use always
provides the complete list of type arguments.

## Specialize a structure

```sx
struct Pair<T> {
    let first:T
    let second:T
}

func main() {
    let pair = Pair<int>(first:1, second:2)
    print(pair.first)
}
```

`Pair<int>` and `Pair<str>` are two distinct concrete types. A method of a
non-generic structure may declare its own type parameters.

## Specialize a class

```sx
class Box<T> {
    let value:T

    init(value:T) {
        self.value = value
    }
}

func main() {
    var box = Box<int>(42)
    print(box.value)
}
```

Every specialization retains ordinary class identity semantics and has its own
static storage. Its methods may use the class parameters, but cannot add a
second list of type parameters.

## Specialize an enum

```sx
enum Outcome<T,E> {
    success(T)
    failure(E)
}

func main() {
    let outcome = Outcome<int,str>.success(42)
    let value = match outcome {
        success(number) => number
        failure(error) => 0
    }
    print(value)
}
```

Variant construction and `match` do not infer enum type arguments: the full
list remains explicit.

Protocol constraints and function inference are described with
[generic functions](../Functions/Generics.md).

[Back to data types](README.md) ·
[Define a protocol](Protocols.md)
