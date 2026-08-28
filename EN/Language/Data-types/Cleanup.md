# Run deterministic cleanup

A `drop` block releases a resource when the value that owns it reaches the end
of its normal lifetime.

## Clean up a structure

```sx
struct File {
    let descriptor:int

    drop {
        print("closing ", self.descriptor)
    }
}

func main() {
    {
        let file = File(descriptor:1)
    }
    print("cleanup complete")
}
```

`drop` runs at the deterministic end of every complete value that has not been
transferred. It does not make the structure non-copyable: after
`var second = first`, `first` and `second` are two live values, and each runs
its own block exactly once.

A structure runs its block before destroying its fields. Those fields are
destroyed in reverse declaration order.

## Clean up a class

```sx
class Connection {
    drop {
        print("closed")
    }
}
```

A class block runs once when the last reachable root disappears. Cycles that
become unreachable are finalized too. The release that makes a component
unreachable starts its cleanup, then owned fields propagate cleanup through
that component.

With inheritance, blocks run from the dynamic class toward its bases. `drop`
is neither virtual nor callable and is followed by no explicit `super` call.

## Clean up every normal exit

`return`, `break`, `continue`, and `try` propagation clean up every scope they
leave. Fatal termination with `panic`, an assertion failure, or a runtime error
does not promise this cleanup.

[Back to data types](README.md) ·
[Copy or transfer a value](../Ownership/Copy-and-move.md)
