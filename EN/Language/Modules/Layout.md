# Map folders to modules

An `@module.sx` or `@Module.sx` file contributes directly to the logical module
represented by its folder. Both spellings have exactly the same meaning, and
their structural name never appears in a source path.

```text
GFX/Module/@module.sx                 → GFX
GFX/Module/GPU/@module.sx             → GFX.GPU
GFX/Module/GPU/@Module.sx             → GFX.GPU
GFX/Module/GPU/Device.sx              → GFX.GPU.Device
Sandbox/MyModule/@Module.sx           → MyModule
```

## Define a module's primary boundary

For visibility rules, the primary module also owns implementation modules
under its folder. An unqualified declaration in `GFX/Module/GPU/Device.sx` is
therefore available to other files under `GFX.GPU`, but not to `GFX.Scene` or
package consumers.

Child paths remain distinct imports: this ownership neither merges files nor
changes their module names.

Without `Package.json`, directly compiling or editing a primary module uses
the parent of its folder as the implicit root. `Sandbox/Test/@Module.sx` thus
retains the identity `Test`: `Package.` sees direct children of `Sandbox`,
while `Package.Test.` reaches children of `Test`.

The primary file follows ordinary rules. It may define a facade with public
re-exports, private helpers, and its own `use` declarations. Importing or
qualifying its logical module loads it.

A flat file and a primary file cannot provide the same module in one root.
`GPU.sx` and `GPU/@module.sx` are therefore incompatible, as are `@module.sx`
and `@Module.sx` in the same folder. Portable, platform, and exact-target roots
may still provide corresponding fragments of the same module.

## Combine a module and its child namespace

A module and modules under the same path form a qualified namespace:

```text
STD/Module/Math.sx       → STD.Math
STD/Module/Math.Vec3.sx  → STD.Math.Vec3
```

The physical path `Math/Vec3.sx` provides the same child module. One import can
then expose both parts on demand:

```sx
use STD.Math

let angle = Math.cos(0.0)
let position = Math.Vec3(x:1.0, y:2.0, z:3.0)
```

A public declaration or public re-export explicitly named `Vec3` in `Math.sx`
takes precedence over the child module of the same name. A private declaration
never hides a public child module from callers.

[Back to modules](README.md) ·
[Compose targeted fragments](Fragments.md)
