# Embed a file in the executable

`embed_text` reads a UTF-8 file during compilation and produces an ordinary
`str` stored in the executable.

```sx
func main() {
    let page = embed_text("Web/index.html")
    print(page)
}
```

The path is relative to the `.sx` file containing the call and must be known at
compile time. An immutable `let` directly bound to a literal is also accepted.
Silex records the file as a cache dependency: changing its contents rebuilds
the executable.

The source file is no longer needed at runtime. `embed_text` rejects invalid
UTF-8 and currently limits each file to 16 MiB. The resulting text may
represent HTML, a shader, configuration, or any other format, without a
consumer-package-specific intrinsic function.

## Embed exact bytes

```sx
func main() {
    let image:uint8[] = embed_bytes("Web/logo.png")
    print(image.count())
}
```

`embed_bytes` accepts every byte, including zero and invalid UTF-8 sequences,
then returns an owned `uint8[]` list. Every evaluation creates independent
storage: modifying one list changes neither the image included in the
executable nor another evaluation's result.

Both functions share the relative-path, compile-time value, cache dependency,
and 16 MiB limit rules.

`embed_text` and `embed_bytes` are reserved language functions. They cannot be
redeclared, used as bindings, or imported under aliases.

[Back to modules](README.md) ·
[Understand intrinsic classes](../Data-types/Intrinsic-classes.md)
