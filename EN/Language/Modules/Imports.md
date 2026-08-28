# Import and qualify a declaration

The beginning of a path indicates where the module comes from:

| Path prefix | Origin | Example |
| --- | --- | --- |
| `Package.` | current package root | `use Package.Math.Vec3` |
| `Module.` | current file's folder | `use Module.Interpolation` |
| global package name | installed or linked package | `use STD.Math` |

The documentation therefore always writes a local path with `Package.` or
`Module.`, and reserves paths such as `STD.Math` or `MyPackage.Math` for
globally available packages. This rule keeps provenance visible and prevents a
future registry package from conflicting with a local folder of the same name.

## Import a module from the current package

This application stores its sources in `Sources/`:

```text
MyApplication/
├── Package.json
└── Sources/
    ├── Main.sx
    └── Math/
        ├── Operations.sx
        └── Vec3.sx
```

At the beginning of `Sources/Main.sx`, anchor imports at the package root:

```sx
use Package.Math.Vec3
use Package.Math.Operations.add as add
```

From this exact file, `Module.Math.Vec3` and
`Module.Math.Operations.add` would also be valid because `Main.sx` is at the
root of `Sources/`. `Package.` better expresses the intent when the path must
not depend on the importing file's folder.

`as` chooses another local name. The `add` alias above creates neither a new
declaration nor a new type.

## Import a neighboring module

With these two files:

```text
Sources/Demo/Main.sx
Sources/Demo/Interpolation.sx
```

`Sources/Demo/Main.sx` directly imports the neighboring declaration:

```sx
use Module.Interpolation.interpolate as interpolate
```

The same file could use `Package.Demo.Interpolation.interpolate`. `Module.` is
relative to the folder containing `Main.sx`, while `Package.` always starts
again from the package's logical root.

In an unnamed application, that root corresponds to the path relative to the
`sources` folder. Without a manifest, it comes from the implicit root selected
from the entry file and remains stable for every module loaded by that run.

## Import a locally linked package

Suppose you develop this package next to your application:

```text
UserPackages/MyPackage/
├── Package.json       (manifest for `MyPackage@0.1.0`)
└── Module/
    └── Math.sx
```

From the folder containing `UserPackages/`, make the package globally
available to your user:

```sh
silex link UserPackages/MyPackage
```

Silex reads its identity, version, and compatibility from `Package.json`, then
keeps a link to that folder without copying its sources. A free-standing
program without a manifest can then import the package by its identity:

```sx
use MyPackage.Math
```

An application with a `Package.json` must additionally declare `MyPackage` as
a direct dependency:

```json
{
  "dependencies": {
    "MyPackage": "=0.1.0"
  }
}
```

The link then selects this working copy if its version satisfies the manifest
constraint. Complete rules are grouped in [package boundaries](Packages.md).
Remove the global link with `silex unlink MyPackage` when it should no longer
replace an installed version.

`Package.MyPackage.Math` would mean something else: `Package.` starts from the
consumer package and never crosses into a dependency.

Paths never contain the physical `.sx` extension. `Package.` and `Module.` are
valid wherever a qualified path is valid, including an import, type, or
expression.

## Load without `use`

A fully qualified path loads the longest accessible module prefix on demand:

```sx
let position = STD.Math.Vec3(x:2, y:10, z:5)
let device = GFX.GPU.Device()
```

The leading package must be globally available. In an application with a
manifest, it must also be a direct dependency. Qualification bypasses neither
dependencies, target selection, nor visibility. It creates no local alias, so
two packages can each own an `ECS` module without collision.

## Use the primary type of a module

When the top-level public type has the same name as the module's final segment,
the module name also designates that type:

```sx
// STD/Math/Vec3.sx declares public struct Vec3.
use STD.Math

let position:Math.Vec3 = Math.Vec3(x:2, y:10, z:5)
```

Do not write `Math.Vec3.Vec3()`. The same rule applies to the fully qualified
path: `STD.Math.Vec3()` directly constructs the public `Vec3` provided by the
`STD.Math.Vec3` module.

[Back to modules](README.md) ·
[Understand file layout](Layout.md)
