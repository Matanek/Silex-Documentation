# Define a package with `Package.json`

`Package.json` lives at the root of an application or package. It describes
the project's sources, required packages, and, for a published package, its
identity and any native files it uses.

| Intention | Fields |
| --- | --- |
| Identify and present the package | `name`, `version`, `description`, `authors`, `requires` |
| Select sources | `sources` |
| Build the package graph | `dependencies`, `devDependencies` |
| Share a namespace | `extensions`, `catalogs` |
| Prepare a native integration | `boundary`, `artifacts` |

An application may need only `sources` and its dependencies. Fields that grant
a namespace, open catalogs, or declare a native boundary belong to a named
package.

## Identify and present the package

A package intended for sharing declares at least its name, version, and Silex
compatibility:

```json
{
  "name": "MyPackage",
  "version": "1.0.0",
  "description": {
    "en": "Reusable utilities for Silex applications.",
    "fr": "Outils réutilisables pour les applications Silex."
  },
  "authors": ["Matanek"],
  "requires": {
    "silex": ">=0.42.0 <0.43.0"
  }
}
```

`name` is the identity used by dependencies and imports. A local package folder
has the same name. `version` uses `MAJOR.MINOR.PATCH` and must match the tag of
a published release.

`requires.silex` begins with an inclusive minimum. The exclusive maximum is
optional. An installed package must declare this compatibility; a local
package under development may still omit it.

### Describe the package in one or more languages

A plain string applies to every language:

```json
{
  "description": "Reusable utilities for Silex applications."
}
```

A localized object maps each language tag to its description, as in the first
example. It must contain `en`. Silex checks the exact language, then its primary
language — `fr` for `fr-FR` — and finally `en`.

Each text must be one non-empty line with no surrounding whitespace. Language
tags are compared case-insensitively and cannot be repeated.

`authors` is an optional array of unique, non-empty names. Their order is
preserved. This field attributes the work; it grants no right over the
registry, namespace, or sources.

## Select sources

`sources` selects the portable source folder relative to the manifest:

```json
{
  "sources": "Sources"
}
```

The default is `Module`. The value `"."` selects the project root. A manifest
accepts exactly one folder, with no absolute path, backslash, glob, array,
empty segment, or internal `.` or `..` segment.

The same value is reused below the `Platform/<OS>/` and `Target/<target>/`
roots. The mapping from these physical files to logical modules is explained
in the language's [package boundaries](../Language/Modules/Packages.md).

## Declare dependencies

`dependencies` contains packages required by distributed code.
`devDependencies` contains only tools used to develop, test, demonstrate, or
measure the package:

```json
{
  "dependencies": {
    "STD": "^0.20.0"
  },
  "devDependencies": {
    "GFX.Viewer": "^0.3.0"
  }
}
```

`^1.4.0` accepts the requested version and newer releases with the same major
number. `=1.4.0` selects exactly that version. This rule also applies during
the `0.x` series: `^0.7.0` accepts `0.8.0`, but not `1.0.0`.

A package cannot appear in both objects. Consumers see only `dependencies` and
must still directly declare every package they import. `devDependencies` join
only the root package's development graph; they do not propagate recursively.

The command that prepares this graph is documented in
[Install and select packages](Install-packages.md#prepare-development-dependencies).

## Authorize composition between packages

`extensions` authorizes separately distributed child packages. `catalogs`
opens facade modules to `contribute` blocks. This is the relevant excerpt from
the GFX manifest:

```json
{
  "extensions": {
    "GFX.Physics": {
      "friend": true
    },
    "GFX.UI": {
      "suite": true
    },
    "GFX.GPU": {
      "friend": true,
      "suite": true
    }
  },
  "catalogs": ["GFX.Components", "GFX.Plugins", "GFX.Resources"]
}
```

An empty entry such as `"GFX.UI": {}` authorizes only the child's identity. An
exact entry may add three independent permissions:

- `friend` opens the parent's `package` declarations to the child;
- `suite` makes the child selectable by the parent's `--suite` installation;
- `merge` allows additive public composition of the exact primary module.

The `Parent.*` wildcard covers direct children only. It may carry `friend`, but
not `suite` or `merge`.

`catalogs` is independent from these permissions: only that field opens named
modules to contributions. The visibility and composition effects are detailed
in [package boundaries](../Language/Modules/Packages.md) and
[re-exports](../Language/Modules/Reexports.md).

## Declare a native boundary

`boundary` describes private native inputs that the compiler may link for one
target. Each branch contains named providers:

```json
{
  "boundary": {
    "macos-arm64": {
      "providers": {
        "Native": {
          "archive": "Boundary/macos-arm64/libNative.a",
          "frameworks": ["CoreFoundation"]
        }
      }
    },
    "linux-x64": {
      "providers": {
        "Native": {
          "archive": "Boundary/linux-x64/libNative.a",
          "libraries": ["m", "pthread"]
        }
      }
    }
  }
}
```

| Provider field | Role |
| --- | --- |
| `archive` | Static archive relative to the package and compatible with the target |
| `frameworks` | Named Apple frameworks, on macOS only |
| `libraries` | Named system libraries, without paths or raw linker options |
| `requires` | Other providers in `Package.Provider` form |

A provider declares at least one of these inputs. It may contain only a system
library, framework, or another provider and then needs no dummy archive. A
requirement may target the package itself or a direct dependency, for example
`"requires": ["GFX.SDL3"]`.

Silex selects only the active target branch. When an archive is declared, its
format and architecture must match that target. The boundary remains private
to the package: consumers see its public Silex API, not foreign archives,
frameworks, libraries, or symbols. Source code that calls a provider is
documented in [Connect a package to a system API](../Language/Interop/README.md).

## Prepare verified artifacts

`artifacts` describes large files distributed outside Git that `silex install`
or `silex link` must prepare. An archive used by `boundary` can therefore be
downloaded to the expected path:

```json
{
  "artifacts": {
    "macos-arm64": {
      "Native": {
        "path": "Boundary/macos-arm64/libNative.a",
        "url": "https://example.com/releases/libNative.a",
        "sha256": "0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef"
      }
    }
  }
}
```

`path` stays inside the package, `url` uses HTTPS, and `sha256` contains the 64
hexadecimal digits of the expected digest. Silex keeps an existing matching
file; otherwise it downloads to a temporary file, verifies the digest, and
moves it into place. Compilation never downloads a file.

`artifacts` therefore prepares a file, while `boundary` decides how that file
participates in native linking. The fields are independent: an archive already
tracked by Git needs no `artifacts` entry.

## Validate the manifest

Before publishing a package, validate its identity, version, and current-target
contract:

```sh
silex check MyPackage
```

To prepare artifacts for another target, then use `--target` with
`silex install` or `silex link`. See
[Develop with local packages](Develop-packages.md) and
[Publish a package in the registry](Publish-package.md) for the complete
workflows.

[Back to tools](README.md) ·
[Install and select packages](Install-packages.md) ·
[Understand package boundaries](../Language/Modules/Packages.md)
