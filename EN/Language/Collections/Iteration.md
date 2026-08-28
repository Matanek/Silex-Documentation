# Traverse values

The binding in a `for` loop specifies whether each element is borrowed,
copied, or written back into its collection.

## Read every element

```sx
func main() {
    let values = [10, 20, 30]

    for value in values {
        print(value)
    }
}
```

Without a modifier, the loop borrows the element for reading during that
iteration. `for let value in values` creates an immutable copy instead.

## Modify stored elements

```sx
for var value in values {
    value += 1
}
```

`for var` writes the final value back before moving to the next element, before
a `continue`, and before a `break`. The collection must be named and mutable.

## Read the index too

```sx
for index, item in values.indexed() {
    print("$(index): $(item)")
}
```

The index is an immutable `int` that starts at `0` and follows collection
order. The element keeps the ordinary modes: `for index, let item` creates a
copy, while `for index, var item` writes the element back. The receiver is
evaluated once, and an empty collection executes no body.

This two-binding form belongs only to `indexed()`; it does not destructure an
arbitrary tuple or another loop source.

## Traverse Unicode text

A string is traversed directly as Unicode `uint32` scalars:

```sx
for scalar in "A🙂é" {
    print(scalar)
}
```

The loop visits `65`, `128578`, then `233`, consistently with `str.count()`.
It decodes UTF-8 on demand without building a list. A scalar is neither an
encoded byte nor necessarily a visible grapheme: use
`STD.Text.UTF8.bytes(text)` for protocol bytes and `STD.Text.Grapheme` for
user-visible units.

A string accepts neither `for var` nor direct `indexed()`. Request an explicit
STD view when positions are part of the operation.

## Traverse a range

`0...3` and `range(3, 0)` exclude their end bound. Bounds are evaluated once,
from left to right; equal bounds execute no iteration. Detailed examples are
provided with [loops](../Control-flow/Conditions-and-loops.md).

## Traverse an application cursor

Any type that exposes exactly one visible instance method `next() T?` can be
used directly as a source:

```sx
struct Countdown {
    var current:int

    func next() int? {
        if self.current == 0 { return null }
        let value = self.current
        self.current--
        return value
    }
}

for value in Countdown(current:3) {
    print(value)
}
```

The source is evaluated once and copied into a private mutable cursor. Write
`move cursor` to explicitly transfer an existing cursor. `next()` is called
once per attempt; a present value executes the body and `null` stops the loop.

If `next` is overloaded, Silex keeps the forms callable without an explicit
argument and returning `T?`; exactly one must remain. A binding without a
modifier borrows the produced value for the body, while `for let` creates an
independent copy.

`for var`, `indexed()`, and the two-binding form are unavailable: a produced
value is not mutable collection storage, and the cursor promises neither an
index nor a known size.

[Back to collections](README.md) ·
[Borrow a view](Views.md)
