# Faire correspondre dossiers et modules

Un fichier `@module.sx` ou `@Module.sx` contribue directement au module logique
représenté par son dossier. Les deux graphies ont exactement le même sens et
leur nom structurel n'apparaît jamais dans un chemin source.

```text
GFX/Module/@module.sx                 → GFX
GFX/Module/GPU/@module.sx             → GFX.GPU
GFX/Module/GPU/@Module.sx             → GFX.GPU
GFX/Module/GPU/Device.sx              → GFX.GPU.Device
Sandbox/MonModule/@Module.sx          → MonModule
```

## Définir la frontière principale d'un module

Le module principal possède aussi les modules d'implémentation placés sous son
dossier pour les règles de visibilité. Une déclaration sans qualification dans
`GFX/Module/GPU/Device.sx` est donc disponible aux autres fichiers sous
`GFX.GPU`, mais pas à `GFX.Scene` ni aux consommateurs du package.

Les chemins enfants restent des imports distincts : cette propriété ne fusionne
pas les fichiers et ne change pas leur nom de module.

Sans `Package.json`, compiler ou éditer directement un module principal prend
le parent de son dossier comme racine implicite. `Sandbox/Test/@Module.sx`
conserve ainsi l'identité `Test` : `Package.` voit les enfants directs de
`Sandbox`, tandis que `Package.Test.` atteint les enfants de `Test`.

Le fichier principal suit les règles ordinaires. Il peut définir une façade
avec des réexportations publiques, des aides privées et ses propres `use`.
Importer ou qualifier son module logique le charge.

Un fichier plat et un fichier principal ne peuvent pas fournir le même module
dans une même racine. `GPU.sx` et `GPU/@module.sx` sont donc incompatibles, tout
comme `@module.sx` et `@Module.sx` dans un même dossier. Les racines portable,
plateforme et cible exacte peuvent toutefois fournir leurs fragments
correspondants d'un même module.

## Combiner un module et son espace enfant

Un module et les modules placés sous le même chemin forment un espace qualifié :

```text
STD/Module/Math.sx       → STD.Math
STD/Module/Math.Vec3.sx  → STD.Math.Vec3
```

Le chemin physique `Math/Vec3.sx` fournit le même module enfant. Un seul import
peut ensuite exposer les deux parties à la demande :

```sx
use STD.Math

let angle = Math.cos(0.0)
let position = Math.Vec3(x:1.0, y:2.0, z:3.0)
```

Une déclaration publique ou une réexportation publique explicitement nommée
`Vec3` dans `Math.sx` l'emporte sur le module enfant du même nom. Une déclaration
privée ne masque jamais un module enfant public pour les appelants.

[Revenir aux modules](README.md) ·
[Composer des fragments ciblés](Fragments.md)
