# Understand package boundaries

A manifest selects a physical source root, `Module/` by default, without adding
that physical name to logical modules. With `"sources": "Sources"`,
`Sources/Geometry/Vec3.sx` provides `Geometry.Vec3` in an unnamed application
and `Math.Geometry.Vec3` in the `Math` package.

The `sources` path is relative to `Package.json`. The value `"."` selects the
package root. Exactly one folder is accepted: absolute paths, backslashes,
empty segments, `.` or `..`, repeated separators, a trailing separator, globs,
and arrays are forbidden.

Inside that root, every `@Name.sx` file contributes to the module represented
by its directory. At the `Module/` root of a `Math` package,
`@Operations.sx` and `@Vectors.sx` therefore both belong to `Math`. Their
physical names can never be imported. `@Module.sx` remains accepted as the old
conventional name, with no special semantics. See the complete
[file-to-module mapping](Layout.md) for the full organization contract.

## Select sources for the target

A package may add roots for the platform and exact target:

```text
Platform/MacOS/Module/
Platform/Linux/Module/
Platform/Windows/Module/
Target/macos-arm64/Module/
Target/linux-x64/Module/
Target/windows-x64/Module/
Target/windows-arm64/Module/
```

Physical segments never appear in module names. A custom `sources` value
replaces the final `Module` folder in all three root families. The platform
root hosts code shared by its architectures; the exact-target root remains
reserved for real architecture or ABI dependencies. Other platforms and
targets are ignored.

Several active roots from one package may contribute additively to the same
module. None replaces another. Portable code reaches specialized declarations
with the same name through `Platform.name` or `Target.name`.

Every fragment may contain a local `main` for direct experimentation. Only the
`main` in the exact file passed as the entry is retained; all others are
ignored and never become module members.

A local file outside public roots can be compiled as an explicit entry. Only
that file becomes available for this compilation: its neighboring folder is
not indexed, and it does not become another fragment of an existing public
module.

## Declare every direct dependency

An application sees only packages it declares directly. A transitive
dependency is never automatically accessible. A folder named `Packages/` has
no special meaning and does not make its neighbors visible.

For an explicit entry, Silex finds the nearest `Package.json` in its folder or
parents. Resolution therefore does not depend on the folder from which the
command is run. With a manifest, declared dependencies replace the implicit
environment of a free-standing program.

`Package` and `Module` are reserved as contextual roots. A package identity
cannot be exactly one of these names or begin with `Package.` or `Module.`.

## Authorize a child package

A qualified name extends the namespace of each of its prefixes. The parent
must explicitly authorize a separately distributed child package. `GFX.*`
authorizes only direct children such as `GFX.UI`, never `GFX.UI.Controls`.

Every exact authorization can grant three independent permissions:

- `friend` opens the parent's `package` declarations to that child;
- `suite` makes that child selectable during explicit parent-suite
  installation;
- `merge` opens only the child's exact primary module to additive public
  composition with the parent's module.

These permissions default to `false`. A wildcard may carry `friend: true`,
deliberately granting access to every matching future child. `suite` and
`merge` are rejected on a wildcard. If an exact entry and wildcard both match,
the exact entry defines all permissions.

Authorization delegates a name, never the parent's authority. If the parent
already provides the exact `GFX.Physics` module, it remains canonical. Without
`merge`, the presence of the child's primary module is a collision. With
`merge`, only public declarations are added, every owner remains known, and
every public collision is rejected. `module` and `package` visibility do not
merge; `friend` remains a separate decision.

The `friend` relation goes from the named child toward the declaring parent. It
neither installs nor activates the child, replaces any dependency, opens any
`module`, `local`, `private`, or `protected` declaration, nor makes anything
public to ordinary consumers. A suite creates no dependency from the parent to
the child.

Facade catalogs open to contributions are explained in
[re-exports](Reexports.md). Installation, development links, versions,
artifacts, and native boundaries belong to the
[Silex tools](../../Tools/README.md), not module semantics.

[Back to modules](README.md) ·
[Compose targeted fragments](Fragments.md)
