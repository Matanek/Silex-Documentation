# Réexporter et contribuer à une façade

Une réexportation publique rend une déclaration accessible depuis un autre
module sans la copier.

```sx
public use Geometry.Types.Vector
public use Geometry.Operations.length
public use Geometry.Operations.distance as measure
```

Sans `as`, la déclaration conserve son nom actuel. Une réexportation expose une
seule déclaration, jamais tout l'arbre d'un module.

Lorsque le module contient un type public du même nom que son dernier segment,
le nom répété peut être omis :

```sx
// Rendering.Renderer.sx déclare public class Renderer.
public use Rendering.Renderer
```

Cette forme équivaut à `public use Rendering.Renderer.Renderer` et ne
réexporte pas l'arbre `Rendering.Renderer`.

## Contribuer à un catalogue parent

Un package enfant qualifié peut ajouter ses propres déclarations publiques à
un catalogue explicitement ouvert par son parent. Les blocs vivent dans le
module principal portable `Module/@Module.sx` de l'enfant :

```sx
contribute GFX.Components {
    public use GFX.Physics.RigidBody2D.RigidBody2D
}

contribute GFX.Resources {
    public use GFX.Physics.World2D.World2D
}
```

Le bloc accepte seulement des `public use` qui nomment des déclarations
possédées par le package contributeur. Il ne peut contenir ni fonction, ni
type, ni champ, ni extension, ni instruction exécutable, ni alias de type. La
composition ajoute donc des noms de façade sans injecter d'implémentation dans
le parent.

Le manifeste parent doit lister exactement chaque catalogue et autoriser déjà
le package enfant avec `extensions`. Seuls les packages présents dans le graphe
résolu contribuent. Une collision avec une déclaration du catalogue, une autre
contribution ou un espace enfant produit une erreur au lieu de choisir un ordre
ou un remplacement.

[Revenir aux modules](README.md) ·
[Exposer ou masquer une déclaration](Visibility.md)
