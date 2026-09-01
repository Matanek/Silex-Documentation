# Run, interpret, or compile a program

Use `silex run` during ordinary development. The command compiles the source
for the host machine, launches the resulting executable, and forwards its exit
code to the terminal.

```sh
silex run Project
```

The directory must directly contain one `.sx` file that declares a top-level
`main` function. The filename is free: `Main.sx` is only a convention. From
inside the application directory, omit the path:

```sh
cd Project
silex run
```

You can still select any `.sx` file explicitly:

```sh
silex run Project/Main.sx
```

This explicit form remains available when several files in the same directory
declare `main`. Directory discovery does not search subdirectories and fails
rather than choosing arbitrarily between multiple entries.

## Choose between the three commands

| Intent | Command |
| --- | --- |
| Compile, then run on the current machine | `silex run [Source.sx\|Directory]` |
| Execute with the reference interpreter | `silex interpret Source.sx` |
| Produce an executable at a chosen location | `silex compile Source.sx -o Application` |

`silex interpret` executes portable IR without producing a native executable.
It is useful for comparing reference behavior with the backend, but cannot
call most system boundaries provided by packages. In that case, use
`silex run`.

`silex compile` only writes the requested program. It does not launch it:

```sh
silex compile Project/Main.sx --output Application
./Application
```

On Windows, normally choose a name ending in `.exe`:

```powershell
silex compile Project/Main.sx --output Application.exe
.\Application.exe
```

## Compile in Release or Debug mode

`run` and `compile` use Release mode by default. This mode applies compiler
optimizations without changing the language's safety rules.

Select Debug when you need to diagnose native code:

```sh
silex run Project/Main.sx --debug
silex compile Project/Main.sx --debug -o Application
```

The short forms are `-d` for Debug and `-r` for Release. Both modes cannot be
requested in the same command.

When a native program is interrupted by a signal, Silex preserves the faulty
executable and prints a Debug reproduction command along with the command for
the debugger available on the host.

## Choose a compilation target

Without `--target`, `silex compile` selects the current machine. Display the
targets recognized by the installed version with:

```sh
silex targets
```

Silex 0.42 recognizes `macos-arm64`, `linux-x64`, `windows-x64`, and
`windows-arm64`. Pass the desired name for cross-compilation:

```sh
silex compile Project/Main.sx \
    --target linux-x64 \
    --output Application-linux-x64
```

A recognized target does not imply that every native operation is already
available. See the [Silex 0.42 limits](../Reference/Current-limits.md) before
distributing an executable built for another system.

## Inspect IR or ignore the cache

`run` and `interpret` accept `--emit-ir` to print textual IR before program
output. `test` also has this option. This representation is intended for
compiler diagnostics; its format is not a stable API.

Compilation commands normally use the cache stored under `.silex`. Add
`--nocache`, or its short form `-n`, only to force a compilation independent
of the cache:

```sh
silex run Project/Main.sx --nocache
```

The [first program](../Learn/First-program.md#understand-the-silex-directory)
explains where this folder is created and how to avoid scattering it.

[Back to the tools](README.md) · [Write and run tests](Tests.md) ·
[Read the CLI reference](CLI-reference.md)
