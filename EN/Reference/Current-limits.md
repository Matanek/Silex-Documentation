# Know the limits of Silex 0.44

The compiler deliberately keeps a focused surface. The following features are
not implemented:

- package lock files;
- C++ interoperability and general, public C interoperability;
- generic operator declarations, or declarations for `==`, comparisons, `%`,
  bitwise operators, and logical operators.

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

Silex 0.44 distributes and natively verifies `macos-arm64`, `macos-x64`,
`linux-arm64`, `linux-x64`, `windows-arm64`, and `windows-x64`. The release
pipeline builds one standalone archive per target, checks its architecture,
reinstalls it through the public script in a clean environment, then executes
`setup`, `compile`, `run`, and `test` on the exact native host.

This distributed-host matrix does not promise complete cross-compilation
between systems. In particular, producing a macOS application from Linux or
Windows would still require Apple's SDKs and distribution terms.

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
