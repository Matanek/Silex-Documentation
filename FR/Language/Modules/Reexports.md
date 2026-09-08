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

## Contribuer à un catalogue ouvert

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
n'ouvre aucun catalogue et n'est pas requise pour une contribution externe.

Tout package nommé peut réexporter les déclarations qu'il possède vers un
catalogue ouvert par l'une de ses dépendances directes. Son nom n'a pas besoin
d'appartenir à l'espace de noms du propriétaire. Un package `AgentTools` peut
par exemple contribuer son plugin à la façade de GFX :

```sx
contribute GFX.Plugins {
    public use AgentTools.Plugin as Agents
}
```

`AgentTools/Package.json` doit déclarer GFX dans `dependencies`. Le bloc se
trouve dans un atome portable du module principal de `AgentTools`. Ni cette
dépendance ni la contribution ne font de `AgentTools` un enfant de GFX et ne
lui accordent de permission `friend`, `suite` ou `merge`.

Le bloc accepte seulement des `public use` qui nomment des déclarations
possédées par le package contributeur. Il ne peut contenir ni fonction, ni
type, ni champ, ni extension, ni instruction exécutable, ni alias de type. La
composition ajoute donc des noms de façade sans injecter d'implémentation dans
le parent.

La cible doit être un module existant possédé par une dépendance directe et
correspondre exactement à une entrée de ses `catalogs`. Seuls les packages
présents dans le graphe résolu contribuent. Une collision avec une déclaration
du catalogue, une autre contribution ou un espace enfant produit une erreur au
lieu de choisir un ordre ou un remplacement.

[Revenir aux modules](README.md) ·
[Exposer ou masquer une déclaration](Visibility.md)
