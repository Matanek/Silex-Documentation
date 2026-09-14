# Write and run a first program

This first program prints a message in the terminal. You only need a
[working Silex installation](../Tools/Installation.md) and a text editor.

## Create the source file

Create a directory for the program, then add a `Main.sx` file:

```sx
func main() {
    print("Hello, Silex!")
}
```

A Silex source uses the `.sx` extension. The `main` function is the program's
entry point. Here, it calls `print` to write one line to the terminal.

## Run the program

If the terminal is in the directory that contains `Main.sx`, run:

```sh
silex run
```

The terminal may also be in another directory. For example, from the parent of
`Project/`:

```sh
silex run Project
```

`silex run` searches the given directory—or the current directory when you
omit the path—for the only `.sx` file that declares a top-level `main`
function. The file may have a name other than `Main.sx`. The search remains
limited to the direct directory and does not descend into subdirectories.

You can also provide the file explicitly:

```sh
silex run Project/Main.sx
```

This form is required when several files in the same directory declare
`main`.

The program prints:

```text
Hello, Silex!
```

`silex run` builds an executable, launches it, and forwards its exit code to the
terminal. This first program needs no project configuration. The
[compilation guide](../Tools/Run-and-compile.md#choose-the-compilation-backend)
explains how to select LLVM or the direct Silex native backend.

## Understand the `.silex` directory

Silex creates a `.silex` directory in the directory from which you run the
command. It stores the compilation cache and the private executable used by
`silex run`, among other artifacts.

To avoid scattering several `.silex` directories, run commands from a stable
project root and pass the application or source path, as in
`silex run Project` above.

If the project uses Git, ignore Silex-generated files by adding this line to
its `.gitignore`:

```text
.silex/
```

[Back to the learning path](README.md) ·
[Understand variables](../Language/Values/Variables.md)
