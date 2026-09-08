# Re-export and contribute to a facade

A public re-export makes a declaration accessible from another module without
copying it.

```sx
public use Geometry.Types.Vector
public use Geometry.Operations.length
public use Geometry.Operations.distance as measure
```

Without `as`, the declaration keeps its current name. A re-export exposes one
declaration, never an entire module tree.

When a module contains a public type with the same name as its final segment,
the repeated name may be omitted:

```sx
// Rendering.Renderer.sx declares public class Renderer.
public use Rendering.Renderer
```

This form is equivalent to `public use Rendering.Renderer.Renderer` and does
not re-export the `Rendering.Renderer` tree.

## Contribute to an open catalog

GFX is currently the only package that uses catalogs. It explicitly opens
three modules in its `Package.json`:

```json
{
  "catalogs": ["GFX.Components", "GFX.Plugins", "GFX.Resources"]
}
```

This `catalogs` property, and only this property, opens these modules to
`contribute` blocks. An `extensions` entry separately controls child-package
authorization and the `friend`, `suite`, or `merge` permissions; it neither
opens a catalog nor is required for an external contribution.

Any named package may re-export declarations it owns into a catalog opened by
one of its direct dependencies. Its name does not need to belong to the owner's
namespace. For example, an `AgentTools` package may contribute its plugin to
the GFX facade:

```sx
contribute GFX.Plugins {
    public use AgentTools.Plugin as Agents
}
```

`AgentTools/Package.json` must declare GFX in `dependencies`. The block lives
in a portable atom of the `AgentTools` primary module. Neither this dependency
nor the contribution makes `AgentTools` a child of GFX or grants it a
`friend`, `suite`, or `merge` permission.

The block accepts only `public use` declarations that name declarations owned
by the contributing package. It cannot contain a function, type, field,
extension, executable statement, or type alias. Composition therefore adds
facade names without injecting implementation into the parent.

The target must be an existing module owned by a direct dependency and must
exactly match one of its `catalogs` entries. Only packages present in the
resolved graph contribute. A collision with a catalog declaration, another
contribution, or a child namespace produces an error instead of selecting an
order or replacement.

[Back to modules](README.md) ·
[Expose or hide a declaration](Visibility.md)
