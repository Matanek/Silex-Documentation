# Importer et qualifier une déclaration

Le début d'un chemin indique d'où vient le module :

| Début du chemin | Origine | Exemple |
| --- | --- | --- |
| `Package.` | racine du package courant | `use Package.Math.Vec3` |
| `Module.` | dossier du fichier courant | `use Module.Interpolation` |
| nom d'un package global | package installé ou lié | `use STD.Math` |

La documentation écrit donc toujours un chemin local avec `Package.` ou
`Module.` et réserve les chemins tels que `STD.Math` ou `MonPackage.Math` aux
packages disponibles globalement. Cette règle garde la provenance visible et
évite qu'un futur package du registre entre en conflit avec un dossier local du
même nom.

## Importer un module du package courant

Cette application place ses sources dans `Sources/` :

```text
MyApplication/
├── Package.json
└── Sources/
    ├── Main.sx
    └── Math/
        ├── Operations.sx
        └── Vec3.sx
```

Au début de `Sources/Main.sx`, ancrez les imports à la racine du package :

```sx
use Package.Math.Vec3
use Package.Math.Operations.add as add
```

Depuis ce fichier précis, `Module.Math.Vec3` et
`Module.Math.Operations.add` seraient aussi valides, car `Main.sx` se trouve à
la racine de `Sources/`. `Package.` exprime toutefois mieux l'intention lorsque
le chemin ne doit pas dépendre du dossier du fichier qui importe.

`as` choisit un autre nom local. L'alias `add` ci-dessus ne crée ni nouvelle
déclaration ni nouveau type.

## Importer un module voisin

Avec ces deux fichiers :

```text
Sources/Demo/Main.sx
Sources/Demo/Interpolation.sx
```

`Sources/Demo/Main.sx` importe directement la déclaration voisine :

```sx
use Module.Interpolation.interpolate as interpolate
```

Le même fichier pourrait employer le chemin
`Package.Demo.Interpolation.interpolate`. `Module.` reste relatif au dossier de
`Main.sx`, tandis que `Package.` repart toujours de la racine logique du
package.

Dans une application sans nom, cette racine correspond au chemin relatif au
dossier `sources`. Sans manifeste, elle vient de la racine implicite choisie
depuis le fichier d'entrée et reste stable pour chaque module chargé par cette
exécution.

## Importer un package lié localement

Supposons que vous développiez ce package à côté de votre application :

```text
UserPackages/MonPackage/
├── Package.json       (manifeste de `MonPackage@0.1.0`)
└── Module/
    └── Math.sx
```

Depuis le dossier qui contient `UserPackages/`, rendez ce package disponible
globalement pour votre utilisateur :

```sh
silex link UserPackages/MonPackage
```

Silex lit l'identité, la version et la compatibilité dans `Package.json`, puis
conserve un lien vers ce dossier sans copier ses sources. Un programme libre,
sans manifeste, peut alors importer le package par son identité :

```sx
use MonPackage.Math
```

Une application qui possède un `Package.json` doit en plus déclarer
`MonPackage` comme dépendance directe :

```json
{
  "dependencies": {
    "MonPackage": "=0.1.0"
  }
}
```

Le lien sélectionne alors cette copie de travail si sa version satisfait la
contrainte du manifeste. Les règles complètes sont regroupées dans
[les frontières des packages](Packages.md). Retirez le lien global avec
`silex unlink MonPackage` lorsqu'il ne doit plus remplacer une version
installée.

`Package.MonPackage.Math` aurait un autre sens : `Package.` repart du package
consommateur et ne permet jamais de traverser vers une dépendance.

Les chemins ne contiennent jamais l'extension physique `.sx`. `Package.` et
`Module.` sont valides partout où un chemin qualifié est valide, notamment
dans un import, un type ou une expression.

## Charger sans `use`

Un chemin complètement qualifié charge à la demande le plus long préfixe de
module accessible :

```sx
let position = STD.Math.Vec3(x:2, y:10, z:5)
let device = GFX.GPU.Device()
```

Le package de tête doit être disponible globalement. Dans une application avec
manifeste, il doit aussi être une dépendance directe. La qualification ne
contourne ni les dépendances, ni la sélection de cible, ni la visibilité. Elle
ne crée aucun alias local, ce qui permet à deux packages de posséder chacun un
module `ECS` sans collision.

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
