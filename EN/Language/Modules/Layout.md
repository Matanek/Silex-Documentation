# Map files to modules

An ordinary file creates a module from its path. A file whose name starts with
`@` expresses a different intention: it contributes to the logical module
represented by its directory.

```text
GFX/Module/GPU/Device.sx     → GFX.GPU.Device
GFX/Module/GPU/@Device.sx    → GFX.GPU
```

The `@` prefix belongs only to the physical filename. It never becomes an
import segment, namespace, or Silex declaration.

## Choose a valid filename

Each module name segment starts with an ASCII letter or `_`, followed by ASCII
letters, digits or `_`. `Test_01.sx` is therefore valid. Dots separate
segments: `Math.Geometry.Point.sx` provides the same module path as
`Math/Geometry/Point.sx`. Directory names that form this path follow the same
rule.

A hyphen or space cannot belong to a module name: `Test-01.sx` is invalid.
The LSP reports this path on the open document, even before its first save,
and keeps suggestions from other valid modules available. Rename the file to
fix the error; compilation remains strict and rejects invalid source paths.

## Split one module across files

A directory may contain several `@Name.sx` files. Silex composes them before
analyzing the module. `STD.Math` uses this organization to separate scalar
operations, vectors, and matrices without creating submodules:

```text
STD/Module/Math/@Scalar.sx    → STD.Math
STD/Module/Math/@Vec2.sx      → STD.Math
STD/Module/Math/@Vec3.sx      → STD.Math
STD/Module/Math/@Mat3.sx      → STD.Math
```

One import exposes the public declarations from all these atoms:

```sx
use STD.Math

let angle = Math.radians(90.0)
let direction = Math.Vec3(x:1.0, y:0.0, z:0.0)
```

`Math.Vec3` here names the public `Vec3` structure declared in `@Vec3.sx`.
The `STD.Math.@Vec3` and `Math.@Vec3` paths do not exist, and editors do not
offer physical atom names as modules.

## Name an atom after its role

Prefer a descriptive name for new files: `@Scalar.sx`, `@Vectors.sx`, or
`@Serialization.sx` immediately says what each file contains.

`@Module.sx`, historically used as a primary file or facade, remains accepted
for backward compatibility. It has no special privilege: Silex composes it
exactly like any other `@Name.sx`. Existing sources may therefore keep it,
while new code can choose more precise names.

## Share the module without merging files

Declarations with `module` visibility—the top-level default—are available
across atoms. A function, type, or enum declared in one atom can therefore be
used directly in another. Importing the module also activates extensions from
all of its atoms.

Each file still retains its own `use` declarations and `local` declarations.
Diagnostics, tests, definition navigation, and asset paths continue to name
the exact physical file.

For visibility purposes, the composed module also owns implementation modules
under its directory. A declaration from `GFX.GPU.Device` can therefore be
visible to `GFX.GPU` without hiding `Device.sx` or changing its import path.

## Avoid competing representations

A directory may contain as many distinct `@Name.sx` atoms as needed. Two
declarations with the same name remain an error: file order never selects a
winner, and one atom never overrides another.

A flat file and source atoms cannot represent the same module in one root.
`GPU.sx` and `GPU/@Device.sx` are therefore incompatible. Portable, platform,
and exact-target roots may still provide their corresponding fragments under
the [targeted composition rules](Fragments.md).

## Create a child module

Without an `@` prefix, the filename remains a module segment:

```text
STD/Module/Math/@Scalar.sx         → STD.Math
STD/Module/Math/Geometry.sx        → STD.Math.Geometry
STD/Module/Math/Geometry/@Shape.sx → STD.Math.Geometry
```

A module and its children form a qualified namespace. A public declaration
explicitly named after a child takes precedence over that child module; a
private declaration does not hide it from callers.

Without `Package.json`, compiling or editing an atom directly uses the parent
of its directory as the implicit root. `Sandbox/Test/@Display.sx` therefore
keeps the `Test` module identity, not `Test.@Display`.

[Back to modules](README.md) ·
[Expose or hide a declaration](Visibility.md)
