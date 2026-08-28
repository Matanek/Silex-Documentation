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

## Contribute to GFX catalogs

GFX is currently the only package that uses catalogs. It explicitly opens
three modules in its `Package.json`:

```json
{
  "catalogs": ["GFX.Components", "GFX.Plugins", "GFX.Resources"]
}
```

This `catalogs` property, and only this property, opens these modules to
`contribute` blocks. An `extensions` entry separately controls child-package
authorization and the `friend`, `suite`, or `merge` permissions; it does not
open a catalog.

A direct child package of GFX may then re-export declarations it owns into one
of these three declared modules. `GFX.Physics` does so as follows:

```sx
contribute GFX.Components {
    public use GFX.Physics.RigidBody2D.RigidBody2D
}

contribute GFX.Resources {
    public use GFX.Physics.World2D.World2D
}
```

The blocks live in a portable atom of the child package's primary module. They
currently reside in `GFX.Physics/Module/@Module.sx`; the atom's name does not
open the catalog.

The block accepts only `public use` declarations that name declarations owned
by the contributing package. It cannot contain a function, type, field,
extension, executable statement, or type alias. Composition therefore adds
facade names without injecting implementation into the parent.

The target must be an existing module owned by GFX and must exactly match a
`catalogs` entry. Only packages present in the resolved graph contribute. A
collision with a catalog declaration, another contribution, or a child
namespace produces an error instead of selecting an order or replacement.

[Back to modules](README.md) ·
[Expose or hide a declaration](Visibility.md)
