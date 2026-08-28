# Expose or hide a declaration

At module level, a declaration without a modifier has `module` visibility. All
files owned by the same composed logical module can use it: its `@Name.sx`
atoms, implementation modules, and selected portable, `Platform`, and `Target`
fragments.

```sx
func helper() {}

public func start() {
    helper()
}

func main() {
    start()
}
```

## Choose a geographic scope

- `public` exposes the declaration to package consumers.
- `package` exposes it to modules in the package and child packages authorized
  with `friend: true`.
- `module` exposes it to the composed logical module; this is the default.
- `local` limits it to the exact source file.

```sx
public struct Position { var x:int }
package struct DecodeState {}
local struct ParserState {}
```

The explicit `module` modifier remains accepted when a boundary needs
emphasis, but omitting it is the usual style.

Two packages sharing a prefix do not share `package` access without `friend`
permission. A `local` declaration never crosses its file. Atoms from one
module share `module` access without merging their lexical scopes or imports;
their physical `@Name` never enters a source path. Without an atom, two sibling
paths remain distinct boundaries.

`private` and `protected` are relative to a type and are therefore rejected at
module level. `private` belongs to the declaring type family; `protected` is
reserved for class members and their descendants.

## Inherit member visibility from the type

A field, constructor, method, static member, or nested type without a modifier
inherits the visibility of its containing type.

```sx
public class Session {
    private let token:str

    init(token:str) {
        self.token = token
    }

    func text() str {
        return self.token
    }

    package func debug() {}
}
```

`Session.init` and `Session.text` are public by inheritance. Type visibility
always caps member visibility: an explicit modifier may preserve or reduce it,
never enlarge it. Writing `public func` inside a class visible only to its
module is an error, not silently capped visibility.

An override or protocol implementation retains the visibility of its concrete
type. A call through an already accessible base type or protocol preserves the
visible contract; the implementation does not need to publish it again.

`use` introduces a name in its file and `public use` re-exports it. Other
visibility modifiers do not apply to `use`.

[Back to modules](README.md) ·
[Re-export a declaration](Reexports.md)
