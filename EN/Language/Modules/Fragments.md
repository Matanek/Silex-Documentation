# Compose portable and targeted fragments

A named package can provide the same relative path in its portable root,
platform root, and exact-target root:

```text
Module/Randomizer.sx
Platform/MacOS/Module/Randomizer.sx
Target/macos-arm64/Module/Randomizer.sx
```

For the active target, these files contribute to the same logical module.
Their lexical scopes never merge, however.

## Reach a specialized fragment

Portable code explicitly names a specialized declaration:

```sx
let handle:Platform.Handle = Platform.open()
let layout:Target.Layout = Target.layout()
```

`Platform` and `Target` are contextual qualifiers derived from the package,
logical module, and target. They require no `use` and do not become exported
namespaces.

Every file retains its own `use` declarations and `local` declarations.
`package` declarations remain shared inside the package. A non-public
specialized declaration is accessible only through its qualifier; a public
declaration contributes to the module's composed interface.

## Preserve package boundaries

This composition normally never crosses a package. Two packages that share a
name prefix cannot provide the same exact module unless the parent grants the
child's exact primary module the `merge: true` permission.

This narrow merge composes only public declarations, preserves the owner of
each declaration, and rejects every public name collision. Platform or target
fragments, deeper modules, and `module` or `package` scopes remain owned by
their original package.

[Back to modules](README.md) ·
[Understand package boundaries](Packages.md)
