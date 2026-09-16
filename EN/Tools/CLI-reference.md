# Read the CLI reference

This page gathers the commands and options accepted by Silex 0.44. Linked
guides explain when to use them and what they produce.

## Programs and tests

```text
silex run [source.sx|directory] [--backend <native|llvm>]
    [-d|--debug|-r|--release] [-n|--nocache] [--emit-ir]
silex interpret <source.sx> [-n|--nocache] [--emit-ir]
silex test <source.sx|directory> [--backend <native|llvm>]
    [-n|--nocache] [--emit-ir]
silex compile <source.sx> [--backend <native|llvm>] [--target <target>]
    [-d|--debug|-r|--release] [-n|--nocache]
    -o|--output <executable>
```

| Option | Commands | Effect |
| --- | --- | --- |
| `--backend` | `run`, `test`, `compile` | explicitly selects `native` or `llvm` |
| `-d`, `--debug` | `run`, `compile` | selects Debug (`run` default) and disables Release optimizations |
| `-r`, `--release` | `run`, `compile` | selects Release (`compile` default) |
| `-n`, `--nocache` | `run`, `interpret`, `test`, `compile` | ignores the cache for this command |
| `--emit-ir` | `run`, `interpret`, `test` | prints textual IR before the result |
| `-o`, `--output` | `compile` | selects the required executable path |
| `--target` | `compile` | selects a recognized target instead of the host |

`run` keeps an explicit `.sx` file as the entry point. With a directory, or
without a path for the current directory, it selects the only directly
contained `.sx` file that declares a top-level `main` function. It does not
search subdirectories. If no file or several files match, the command fails
and asks for an explicit source path.

Without `--backend`, macOS ARM64 selects LLVM; the other distributed hosts
select the native backend. LLVM is currently qualified only on `macos-arm64`.
An explicit selection that is unavailable fails without executing the other
backend. `interpret` is not a compilation backend and therefore rejects this
option.

See [Run, interpret, or compile a program](Run-and-compile.md) and
[Write and run tests](Tests.md).

## Installation and packages

```text
silex install <package|package-directory> [--suite] [--dev]
    [--target <target>]
silex check <package-directory>
silex register <package-directory>
silex link <package-directory> [--workspace <directory>]
    [--target <target>]
silex unlink <package-name> [--workspace <directory>]
silex packages
silex packages resolve [source.sx|project-directory]
```

| Option | Commands | Effect |
| --- | --- | --- |
| `--suite` | `install` | installs compatible suite extensions of a registered package |
| `--dev` | `install` | adds development dependencies of the requested package |
| `--target` | `install`, `link` | selects the target used to prepare the package |
| `--workspace` | `link`, `unlink` | limits the link to the given working folder |

`packages` lists installed versions and user links. `packages resolve` prints
the graph actually selected for the given path; without a path, it analyzes
the current folder.

See [Install and select packages](Install-packages.md),
[Develop with local packages](Develop-packages.md), and
[Publish a package](Publish-package.md).

## Compiler and editors

```text
silex setup
silex update
silex targets
silex version
silex lsp
```

`setup`, `update`, `targets`, `version`, and `lsp` accept no argument.
`silex lsp` speaks LSP over standard streams; it is normally launched by an
editor extension, not directly in an interactive terminal.

The following general forms are also available:

```text
silex
silex --help
silex --version
silex -V
```

Without a command, Silex displays the same help as `--help`.

See [Update and maintain Silex](Maintain-Silex.md) and the installation pages
for [Zed](Install-Zed-extension.md) or
[VS Code](Install-VSCode-extension.md).

[Back to the tools](README.md)
