# Update and maintain Silex

Display the version actually found in your `PATH` before diagnosing a behavior
difference:

```sh
silex version
```

`silex --version` and `silex -V` produce the same information. To learn the
machine target and other recognized targets, run:

```sh
silex targets
```

## Update the compiler

Replace an official installation with the latest published version using:

```sh
silex update
```

Silex preserves the folder of the currently running binary. It downloads the
new distribution, verifies its SHA-256 checksum, then replaces the executable.
On Windows, replacement finishes in the background after the command stops so
the file can be released.

The `update` command always targets the latest published version. A
`SILEX_VERSION` variable left in the terminal does not block this explicit
update.

## Install supporting tools

Programs without a native boundary work after installing the Silex executable
alone. Before compiling HLSL shaders or using a package that requires native
linking, prepare the verified toolchain once:

```sh
silex setup
```

Silex installs host-appropriate tools under the user `.silex/toolchain`
folder. Running it again does not download tools that are already present and
valid. They remain compiler details, not dependencies to declare in the
application.

## Distinguish the two `.silex` folders

| Location | Main contents |
| --- | --- |
| `.silex/` in the working folder | compilation cache, private `run` and `test` executables, workspace links |
| `~/.silex/` for the user | installed packages, user links, compiler tools, registry cache |

On Windows, the user folder is `%USERPROFILE%\.silex`. Deleting a project's
`.silex` only forces its artifacts to be regenerated and removes its workspace
links. Global packages and tools remain separate.

## Remove Silex

Delete the executable from the folder chosen during installation:
`~/.local/bin/silex` by default on macOS and Linux, or
`%LOCALAPPDATA%\Silex\bin\silex.exe` on Windows.

The user `.silex` folder is not deleted automatically. You may retain it for a
later reinstallation. Delete it only if you also want to erase your account's
packages, links, tools, and caches.

[Back to the tools](README.md) · [Install Silex](Installation.md) ·
[Read the CLI reference](CLI-reference.md)
