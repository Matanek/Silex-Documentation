# Importer et qualifier une déclaration

Chaque fichier source contribue à un module dérivé de son chemin logique :

```text
Math/Vec3.sx       → Math.Vec3
Math.Geometry.sx   → Math.Geometry
```

`use` introduit dans le fichier le dernier nom d'un module ou d'une
déclaration :

```sx
use Math.Vec3
use Math.Operations.add as add
```

`as` choisit un autre nom local. Un alias est une commodité de nommage ; il ne
crée ni nouvelle déclaration ni nouveau type.

## Ancrer un chemin dans le package ou le dossier courant

Dans une application dont le manifeste choisit `Sources/` :

```text
Sources/Demo/Main.sx
Sources/Demo/Interpolation.sx
```

les trois chemins suivants désignent le même module canonique
`Demo.Interpolation` :

```sx
use Demo.Interpolation.interpolate as canonical_interpolate
use Package.Demo.Interpolation.interpolate as package_interpolate
use Module.Interpolation.interpolate as module_interpolate
```

`Package.` commence à la racine logique du package courant. Dans une
application sans nom, cette racine correspond au chemin relatif au dossier
`sources`. Sans manifeste, elle vient de la racine implicite choisie depuis le
fichier d'entrée et reste stable pour chaque module chargé par cette exécution.

`Module.` commence au dossier logique du fichier qui contient le chemin. Aucun
de ces deux ancrages ne peut traverser une dépendance. Un chemin sans ancrage
reste canonique et peut désigner le package courant ou une dépendance
directement accessible.

Dans un package nommé, `Package.` omet le nom propre du package. Au sein de STD,
`STD.UUID`, `Package.UUID` et, depuis un fichier voisin, `Module.UUID`
sélectionnent donc tous `STD.UUID`.

Les chemins ne contiennent jamais l'extension physique `.sx`. `Package.` et
`Module.` sont valides partout où un chemin qualifié est valide, notamment
dans un import, un type ou une expression. Ils ne créent aucune identité
supplémentaire : plusieurs écritures d'un même fournisseur ne le compilent
qu'une fois.

## Charger sans `use`

Un chemin complètement qualifié charge à la demande le plus long préfixe de
module accessible :

```sx
let position = STD.Math.Vec3(x:2, y:10, z:5)
let device = GFX.GPU.Device()
```

Le package de tête doit être une dépendance directe accessible. La qualification
ne contourne ni les dépendances, ni la sélection de cible, ni la visibilité.
Elle ne crée aucun alias local, ce qui permet à deux packages de posséder chacun
un module `ECS` sans collision.

## Employer le type principal d'un module

Lorsque le type public de premier niveau porte le même nom que le dernier
segment du module, le nom du module désigne aussi ce type :

```sx
// STD/Math/Vec3.sx déclare public struct Vec3.
use STD.Math

let position:Math.Vec3 = Math.Vec3(x:2, y:10, z:5)
```

N'écrivez pas `Math.Vec3.Vec3()`. La même règle vaut pour le chemin entièrement
qualifié : `STD.Math.Vec3()` construit directement le `Vec3` public fourni par
le module `STD.Math.Vec3`.

[Revenir aux modules](README.md) ·
[Comprendre la disposition des fichiers](Layout.md)
