# Install and select packages

Install a public package with its registered name:

```sh
silex install STD
```

Silex selects the latest published version that remains compatible with the
compiler version. To require a precise version, add it after `@`:

```sh
silex install STD@0.19.0
```

Dependencies declared by that version are resolved and installed
automatically. Installation never modifies your application's `Package.json`.

## Declare the package used by a project

An application with a manifest must directly declare every package it imports:

```json
{
  "dependencies": {
    "STD": "^0.19.0"
  }
}
```

`^0.19.0` accepts a published version equal to or newer than `0.19.0` without
crossing the next major version. Use `=0.19.0` when the project requires that
exact version.

A transitive dependency is not automatically visible in your code. The
[Define a package with `Package.json`](Package-manifest.md) page explains the
manifest and version constraints. Their effects on module visibility and
identity are described in [package boundaries](../Language/Modules/Packages.md).

## Install a local copy

A command can also install the current state of a package folder:

```sh
silex install UserPackages/MyPackage
```

This operation copies one version into the user store. Later changes to the
source folder are therefore not included. To work directly on its files, use a
[development link](Develop-packages.md) instead.

## Prepare development dependencies

`--dev` adds the `devDependencies` of the explicitly requested package:

```sh
silex install MyPackage --dev
```

Their ordinary dependencies are installed, but their own `devDependencies`
are not installed recursively. Without `--dev`, these testing, example, or
benchmark tools remain absent from the graph consumed by package users.

## Install an official suite

Some packages declare separately published extensions as members of their
suite. Install the package and compatible members with:

```sh
silex install GFX --suite
```

Installation expands to the suite only for a registry name. This option is
unavailable when the operand is a local folder, and a transitive dependency
never triggers it implicitly.

`--suite` and `--dev` express two independent intentions and can be combined.

## Prepare another target

Installation selects the current machine target by default. When a package
provides artifacts for another target, specify it:

```sh
silex install MyPackage --target windows-x64
```

Recognized targets are displayed by `silex targets`.

## See available and selected packages

Display installed versions and user-wide links:

```sh
silex packages
```

Each line gives the name, version, origin, and path. Possible origins notably
include `installed` and `user-link`.

To inspect the graph actually selected for a source or project, use:

```sh
silex packages resolve Project/Main.sx
silex packages resolve Project
```

The second command accounts for the nearest manifest, versions, development
links, and project development dependencies.

## Remove an installed version

Silex 0.42 has no package uninstall command. The output of `silex packages`
gives the exact path of every version. Delete only the affected
`Name@MAJOR.MINOR.PATCH` folder under `~/.silex/packages`, or under
`%USERPROFILE%\.silex\packages` on Windows.

This deletion modifies no manifest. A project requiring that version must
therefore install another compatible version before it can be resolved again.

[Back to the tools](README.md) ·
[Develop with local packages](Develop-packages.md) ·
[Publish a package](Publish-package.md)
