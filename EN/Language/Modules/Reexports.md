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

## Contribute to a parent catalog

A qualified child package may add its own public declarations to a catalog
explicitly opened by its parent. Blocks live in the child's portable primary
module, `Module/@Module.sx`:

```sx
contribute GFX.Components {
    public use GFX.Physics.RigidBody2D.RigidBody2D
}

contribute GFX.Resources {
    public use GFX.Physics.World2D.World2D
}
```

The block accepts only `public use` declarations that name declarations owned
by the contributing package. It cannot contain a function, type, field,
extension, executable statement, or type alias. Composition therefore adds
facade names without injecting implementation into the parent.

The parent manifest must list every catalog exactly and must already authorize
the child package with `extensions`. Only packages present in the resolved
graph contribute. A collision with a catalog declaration, another
contribution, or a child namespace produces an error instead of selecting an
order or replacement.

[Back to modules](README.md) ·
[Expose or hide a declaration](Visibility.md)
