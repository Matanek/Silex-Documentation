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

## Contribuer aux catalogues de GFX

À ce jour, GFX est le seul package qui utilise des catalogues. Il ouvre
explicitement trois modules dans son `Package.json` :

```json
{
  "catalogs": ["GFX.Components", "GFX.Plugins", "GFX.Resources"]
}
```

C'est cette propriété `catalogs`, et elle seule, qui ouvre ces modules aux
blocs `contribute`. Une entrée dans `extensions` règle séparément l'autorisation
d'un package enfant et ses permissions `friend`, `suite` ou `merge` ; elle
n'ouvre aucun catalogue.

Un package enfant direct de GFX peut alors réexporter les déclarations qu'il
possède vers l'un des trois modules déclarés. `GFX.Physics` le fait ainsi :

```sx
contribute GFX.Components {
    public use GFX.Physics.RigidBody2D.RigidBody2D
}

contribute GFX.Resources {
    public use GFX.Physics.World2D.World2D
}
```

Les blocs se trouvent dans un atome portable du module principal du package
enfant. Ils sont actuellement placés dans `GFX.Physics/Module/@Module.sx` ; le
nom de cet atome n'ouvre pas le catalogue.

Le bloc accepte seulement des `public use` qui nomment des déclarations
possédées par le package contributeur. Il ne peut contenir ni fonction, ni
type, ni champ, ni extension, ni instruction exécutable, ni alias de type. La
composition ajoute donc des noms de façade sans injecter d'implémentation dans
le parent.

La cible doit être un module existant possédé par GFX et correspondre exactement
à une entrée de `catalogs`. Seuls les packages présents dans le graphe résolu
contribuent. Une collision avec une déclaration du catalogue, une autre
contribution ou un espace enfant produit une erreur au lieu de choisir un ordre
ou un remplacement.

[Revenir aux modules](README.md) ·
[Exposer ou masquer une déclaration](Visibility.md)
