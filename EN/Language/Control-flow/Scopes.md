# Explicitly limit a scope

A bare block creates an anonymous lexical scope. It runs exactly once and
produces no value.

```sx
struct Counter {
    var value:int
    func increment() { self.value++ }
}

func main() {
    var counter = Counter(value:0)
    {
        let increment:func() = counter.increment
        increment()
    }

    counter.value += 1
    print(counter.value)
}
```

Here, the scope releases the borrow retained by the bound method before the
next direct access to `counter`. Its variables disappear after `}`, and their
cleanup runs before every normal or transferred exit from the block.

Successive scopes may reuse the same local names. The block has no trailing
semicolon. It does not intercept control flow: `break` and `continue` still
target the nearest enclosing loop, while `return` always exits the function.

[Back to execution control](README.md) ·
[Understand callback borrows](../Functions/Callbacks.md)
