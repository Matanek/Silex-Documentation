# Bind and call a C function

A package imports `Interop.C` and `Interop.Boundary`, then declares a binding
known at compile time at module level:

```sx
use Interop.C
use Interop.Boundary

let write = C.function<
    func(int32, C.Pointer<uint8>, C.Size) C.SignedSize
>(
    library:Boundary.System,
    name:"write"
)
```

`System` is a provider declared by the package for every supported target.
This `let` creates no mutable global state and runs no initializer.
`C.function` applies the target's C calling convention.

The name remains the one documented by the API, here `write`. The selected
provider supplies link entries without exposing them in the public Silex API.

## Call it like an ordinary function

```sx
func write_text(text:str) C.SignedSize {
    return write(
        1,
        C.pointer(text),
        C.byte_count(text)
    )
}
```

The raw result follows the C contract. The binding must handle partial writes,
system errors, and their conversion to its public error type.

A C function without a result uses `void`. Calling it as a statement creates
no hidden Silex value. Unsigned byte parameters and results use exactly
`uint8`; declaring an eight-bit C `Boolean` result as `uint32` violates the
ABI.

The accepted surface remains deliberately narrow: explicitly sized integers
and floats, `C.Pointer<T>`, `C.MutablePointer<T>`, and `void` returns. Memory
details are grouped in [direct exchanges](Memory.md).

[Back to interoperability](README.md) ·
[Know the limits](Limits.md)
