# Provide a callback or call a C address

A system API can call a named Silex function or provide its own function table.

## Expose a named function

`C.function_address(callback)` returns the entry address of a concrete
function. A generic function is explicitly specialized with
`C.function_address<T...>(callback)`.

`C.object_address(value)` and `C.object_from_address<T>(address)` carry class
identity through the opaque context of a system callback. The object must
remain alive until the system has finished using this context.

These operations are reserved for private adapters. They do not turn addresses
into public language values.

On Windows X64, the function address points to a generated Win64 adapter. It
translates callback registers to the portable internal convention and
preserves Win64 nonvolatile registers. Ordinary calls through a function value
use the same adapter in the other direction, so the callback remains callable
by both the system and Silex.

## Call a table entry

```sx
let method = C.load<uint>(vtable, 24 as uint)
let result = C.call<func(uint, int32) int32>(method, object, value)
```

`C.call<func(...) T>` applies the target's C ABI to a `uint` function address.
Its parameters and result share the narrow `C.function` surface: integer or
floating-point scalars, `C.Pointer<T>`, `C.MutablePointer<T>`, and `void`
returns. The signature is checked at compile time.

Resolving the table, keeping its owner alive, and validating the address remain
entirely the adapter's responsibility.

[Back to interoperability](README.md) ·
[Know the limits](Limits.md)
