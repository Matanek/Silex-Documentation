# Connect a package to a system API

`Interop` is the low-level boundary used to build Silex bindings. An
application normally depends on a portable package such as STD and never
manipulates this surface directly.

A named package owns every foreign provider it uses. Its targeted manifest may
select a precompiled archive, named system libraries, Apple frameworks, or a
combination of these entries. A provider that calls only the platform needs no
dummy archive. It remains private to the declaring package.

The compiler validates whether declared scalars and pointers can cross the
target ABI. It maintains no catalog of package symbols: adding a function whose
ABI shape is already supported changes only the package source and, when
needed, its manifest.

- [Bind and call a C function](Functions.md)
- [Exchange text, scalars, and private structures](Memory.md)
- [Provide a callback or call an address](Callbacks.md)
- [Know the currently supported boundary](Limits.md)

The older `MacOS`, `Linux`, and `Windows` namespaces remain toolchain
compatibility contracts. New package bindings use `Boundary.<Provider>`.

[Back to language concepts](../README.md)
