# Write and run tests

A `test` block describes an independent scenario. Place it at the root of an
`.sx` file, then check the expected result with `assert`:

```sx
func double(value:int) int {
    return value * 2
}

test "double an integer" {
    assert(double(21) == 42)
}
```

Run all blocks in the file with:

```sh
silex test Tests/Numbers.sx
```

A successful run prints the scenario followed by a summary:

```text
ok - double an integer
1 passed; 0 failed
```

The command returns a nonzero code when a test fails or a source cannot be
compiled. A failure stops the affected block, but later blocks keep running.
Every compiled scenario is isolated in its own process, so a `panic` or signal
termination cannot interrupt the entire test run.

`test` uses the native backend by default on every host. LLVM remains
explicitly available on macOS ARM64 for comparison or diagnosis:

```sh
silex test Tests/Numbers.sx --backend native
silex test Tests/Numbers.sx --backend llvm
```

## Test an entire folder

Pass a folder to recursively discover `.sx` files containing at least one
`test` block:

```sh
silex test Tests
```

Files run in lexicographic order. Platform or target roots that do not match
the current machine are ignored. A folder without tests succeeds with a report
containing zero tests.

In a folder report, each scenario is prefixed with its source's relative path:

```text
ok - Numbers.sx :: double an integer
```

Only blocks in the selected physical file are activated. Tests placed in its
dependencies are not run indirectly.

## Name and isolate scenarios

The description is optional. An anonymous block is identified by its line:

```sx
test {
    assert(2 + 2 == 4)
}
```

Every block may declare its own local functions. They are visible only in that
scenario and do not capture its variables: pass their data as parameters.

```sx
test "add two values" {
    func add(left:int, right:int) int {
        return left + right
    }

    assert(add(20, 22) == 42, "the expected sum is 42")
}
```

`test` blocks do not belong to the ordinary program: `run`, `interpret`, and
`compile` add them neither to IR nor the executable.

## Reproduce a test without the cache

Silex keeps its private test artifacts under `.silex/test`. To force their
reconstruction, use:

```sh
silex test Tests --nocache
```

`--emit-ir` also prints the IR of every selected source before its report.
These two options mainly serve diagnostics; the ordinary command remains
`silex test <file|folder>`.

[Back to the tools](README.md) ·
[Run, interpret, or compile a program](Run-and-compile.md) ·
[Read the CLI reference](CLI-reference.md)
