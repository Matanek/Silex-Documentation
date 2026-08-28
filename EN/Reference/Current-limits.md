# Know the limits of Silex 0.42

The compiler deliberately keeps a focused surface. The following features are
not implemented:

- package lock files;
- C++ interoperability and general, public C interoperability.

## Annotate some list literals

List literal inference remains syntactic. A non-empty list whose first item is
a variable or an expression without an immediately visible type needs an
annotation:

```sx
let values:Pair<int>[] = [first, second]
```

Removing this annotation will require moving collection type construction from
the parser to typed analysis.

## Distinguish target emission from execution

Silex 0.42 distributes and natively verifies `macos-arm64`, `linux-x64`, and
`windows-x64`. The release pipeline builds a standalone distribution and runs
its smoke test on each corresponding system.

The `windows-arm64` target is recognized and has an emitter, but remains
experimental: no 0.42 artifact is distributed, and execution still awaits
validation on actual Windows ARM64 hardware.

The interpreter remains the reference behavior where both paths cover the same
operation.

## Keep implementation details private

The language exposes no stable ABI, native layout, compiler IR, linker
configuration, allocation, or execution handle. Low-level C operations remain
reserved for package boundary implementations.

A provider may use named system libraries or Apple frameworks without an
archive. A supplied archive must match the target—Mach-O ARM64, ELF X64, COFF
X64, or COFF ARM64. Arbitrary library paths and runtime-loaded providers remain
unavailable.

An adapter may call an already resolved table entry with
`C.call<func(...) T>` without exposing dynamic loading or C types to
applications.

[Back to the reference](README.md) ·
[Know the interoperability limits](../Language/Interop/Limits.md)
