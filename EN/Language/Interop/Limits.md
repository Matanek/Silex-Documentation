# Know the current interoperability boundary

The Silex 0.44 surface is deliberately limited to private package adapters.

## Targets and providers

Implemented STD slices compose `macos-arm64`, `macos-x64`, `linux-arm64`,
`linux-x64`, `windows-arm64`, and `windows-x64`. A `Boundary.<Provider>` is
selected by target and may combine an archive, named system libraries, and
Apple frameworks. Historical toolchain providers remain accepted for
compatibility.

Implemented capabilities cover named or addressed C calls; random seeds;
monotonic and local civil clocks; byte I/O; terminals; files, metadata, and
subprocesses; file systems; sockets; name resolution; system threads; and the
typed Objective-C messages needed by the macOS system WebView.

On Windows, console bindings cover UCRT bytes, console modes, UTF-8 input code
pages, handle waits, and screen-buffer dimensions. Their PE imports and
behavior are verified natively on X64 and ARM64.

macOS uses the fixed `__open` and `__ioctl` facades where public C functions
are variadic under the Apple ARM64 ABI.

## Execution and pointers

`silex run` builds and runs the host's native target, so a platform boundary
works without a separate manual compilation. The explicit reference path
`silex interpret` emulates only `arc4random` and rejects other boundaries.

Accepted pointer sources are the read-only UTF-8 bytes of a `str`, a private
mutable string buffer for system byte output, and stable scalar or fixed-array
storage for `C.MutablePointer<T>`.

## Missing capabilities

General retained pointers, capturing callbacks, first-class C structures,
variadic calls, arbitrary library paths, and public foreign providers are not
implemented. Named callbacks with opaque class context exist for thread
adapters.

A raw C structure remains fixed contiguous scalar storage, private to a
platform module and accompanied by its explicit layout. The distribution
matrix verifies system adapters on all six hosts without exposing their ABIs
or layouts to applications.

## Hide the platform behind a Silex API

The owning package imports `Interop.Boundary` and names the provider from its
active manifest. A target without a corresponding declaration produces a
missing-boundary diagnostic; source code never switches to `MacOS`, `Linux`,
or `Windows`.

A portable module and its platform fragment can compose one common API. For
example, `STD.Randomizer` keeps its algorithm in the portable fragment and
receives `Platform.system_seed()` from a private fragment. Callers manipulate
`Randomizer`, never `arc4random`, `getrandom`, or `ProcessPrng`.

The `Platform` qualifier identifies the physical origin without creating a
public module or requiring an import.

[Back to interoperability](README.md) ·
[Compose targeted fragments](../Modules/Fragments.md)
