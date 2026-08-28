# Understand an intrinsic class

An `intrinsic class` publishes a source API whose storage and behavior are
provided by the Silex compiler.

```sx
public intrinsic class Resources {
    public func insert<T>(value:T)
    public func has<T>() bool
}
```

Its methods declare their signatures without bodies. The contract remains
discoverable without presenting a fake implementation or runtime failure as
ordinary source code.

Application packages cannot use `intrinsic` as a general implementation or
interoperability mechanism. The compiler recognizes a closed set of canonical
classes and validates their complete signatures. An unknown intrinsic class, a
method body, or a signature different from the compiler contract produces a
compilation error.

An intrinsic class declares no field, constructor, `drop`, inheritance, or
protocol conformance. These details belong to the supplied implementation. A
consumer constructs and calls an intrinsic class like any ordinary public
class.

[Back to data types](README.md) ·
[Inspect a value's metadata](Reflection.md)
