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

Pass the source path to `silex run`. If the terminal is in the directory that
contains `Main.sx`, run:

```sh
silex run Main.sx
```

The terminal may also be in another directory. For example, from the parent of
`Project/`:

```sh
silex run Project/Main.sx
```

The program prints:

```text
Hello, Silex!
```

`silex run` builds a native executable, launches it, and forwards its exit code
to the terminal. This first program needs no project configuration.

## Understand the `.silex` directory

Silex creates a `.silex` directory in the directory from which you run the
command. It stores the compilation cache and the private executable used by
`silex run`, among other artifacts.

To avoid scattering several `.silex` directories, run commands from a stable
project root and pass the source path, as in `silex run Project/Main.sx` above.

If the project uses Git, ignore Silex-generated files by adding this line to
its `.gitignore`:

```text
.silex/
```

[Back to the learning path](README.md) ·
[Understand variables](../Language/Values/Variables.md)
