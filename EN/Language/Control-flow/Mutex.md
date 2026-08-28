# Protect shared state

A `mutex` block performs a shared modification while holding the single
critical-section lock of the Silex process.

```sx
func main() {
    var pending:int[] = []

    mutex {
        pending.append(42)
    }

    print(pending[0])
}
```

The lock is recursive: protected code may call a function that opens another
`mutex` block. Silex releases it on every exit from the block, including
`return`, `break`, `continue`, or recoverable `try` propagation.

There is deliberately no manual `lock` or `unlock` operation. The lexical
scope of the block guarantees that an early exit cannot leave the lock held.

[Back to execution control](README.md) ·
[Return a recoverable error](../Functions/Result.md)
