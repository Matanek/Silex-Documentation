# Exchange text and scalars with C

Interop pointers are ephemeral capabilities valid only as direct arguments to
a foreign call. They cannot be stored, returned, or retained by the called API.

## Read the bytes of a string

`C.pointer(text)` exposes the string's UTF-8 bytes for reading during the call.
`C.byte_count(text)` returns their count as `C.Size`, while `text.count()`
counts Unicode scalars. `C.byte_at(text, index)` reads a `uint8` and requires an
index smaller than the byte count.

These operations may also serve a portable algorithm defined in bytes, without
any system library.

An API expecting zero-terminated UTF-8 uses `C.terminated_pointer(text)`. Its
lifetime remains that of the direct call, but a zero byte is guaranteed after
the string without being included in `C.byte_count(text)`.

## Let the system write a scalar

```sx
var seed:uint32 = 0
let written = getrandom(C.mutable_pointer(seed), 4 as C.Size, 0)
```

`C.mutable_pointer` accepts a stable integer or floating-point variable, or a
fixed array of such scalars. The address remains valid only for the direct
foreign call.

## Adapt a byte buffer

A system API cannot directly consume a `uint8[..]` view: Silex collection
elements follow a private layout, not that of a C array.

A platform adapter first compacts the view with `C.string`, then may expose
that private `var str` with `C.mutable_string_pointer(buffer)` during a call.
After a write, it reads bytes again with `C.byte_at` and copies them to the
public mutable view.

This operation is reserved for a freshly allocated, unaliased platform buffer.
It never makes ordinary Silex strings mutable.

## Read a private layout

`C.load<T>(address, byte_offset)` and
`C.store<T>(address, byte_offset, value)` read or write integer or
floating-point scalars at an explicit offset. `C.store` produces the written
value.

These primitives serve private layouts such as `sockaddr`. They expose neither
their memory nor layout in the package's public API.

[Back to interoperability](README.md) ·
[Provide a callback](Callbacks.md)
