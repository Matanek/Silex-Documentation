# Faire correspondre fichiers et modules

Un fichier ordinaire crée un module à partir de son chemin. Un fichier dont le
nom commence par `@` suit une autre intention : il contribue au module logique
représenté par son dossier.

```text
GFX/Module/GPU/Device.sx     → GFX.GPU.Device
GFX/Module/GPU/@Device.sx    → GFX.GPU
```

Le préfixe `@` appartient seulement au nom physique du fichier. Il ne devient
jamais un segment d'import, un espace de noms ou une déclaration Silex.

## Répartir un module entre plusieurs fichiers

Un dossier peut contenir plusieurs fichiers `@Nom.sx`. Silex les compose avant
d'analyser le module. `STD.Math` utilise cette organisation pour séparer les
opérations scalaires, les vecteurs et les matrices sans créer de sous-modules :

```text
STD/Module/Math/@Scalar.sx    → STD.Math
STD/Module/Math/@Vec2.sx      → STD.Math
STD/Module/Math/@Vec3.sx      → STD.Math
STD/Module/Math/@Mat3.sx      → STD.Math
```

Un seul import donne accès aux déclarations publiques de tous ces atomes :

```sx
use STD.Math

let angle = Math.radians(90.0)
let direction = Math.Vec3(x:1.0, y:0.0, z:0.0)
```

`Math.Vec3` désigne ici la structure publique `Vec3` déclarée dans
`@Vec3.sx`. Les chemins `STD.Math.@Vec3` et `Math.@Vec3` n'existent pas, et les
éditeurs ne proposent pas les noms physiques des atomes comme modules.

## Nommer un atome selon son rôle

Préférez un nom descriptif pour les nouveaux fichiers : `@Scalar.sx`,
`@Vectors.sx` ou `@Serialization.sx` indiquent immédiatement ce qu'ils
contiennent.

`@Module.sx`, historiquement utilisé comme fichier principal ou façade, reste
accepté pour la rétrocompatibilité. Il n'a plus de privilège particulier :
Silex le compose exactement comme n'importe quel autre `@Nom.sx`. Les sources
existantes peuvent donc le conserver, tandis que le nouveau code peut choisir
des noms plus précis.

## Partager le module sans fusionner les fichiers

Les déclarations de visibilité `module` — la visibilité par défaut au niveau
supérieur — sont accessibles entre les atomes. Une fonction, un type ou une
enum déclarés dans un atome peuvent donc être utilisés directement dans un
autre. Importer le module active aussi les extensions fournies par tous ses
atomes.

Chaque fichier conserve cependant ses propres `use` et déclarations `local`.
Les diagnostics, les tests, la navigation vers une définition et les chemins
d'assets continuent de désigner le fichier physique exact.

Le module composé possède aussi les modules d'implémentation placés sous son
dossier pour les règles de visibilité. Une déclaration de `GFX.GPU.Device`
peut ainsi être visible par `GFX.GPU` sans rendre `Device.sx` invisible ni
changer son chemin d'import.

## Éviter les représentations concurrentes

Un dossier peut contenir autant d'atomes `@Nom.sx` distincts que nécessaire.
Deux déclarations de même nom restent une erreur : l'ordre des fichiers ne
choisit jamais de gagnant et aucun atome n'en remplace un autre.

Un fichier plat et des atomes ne peuvent pas représenter le même module dans
une même racine. `GPU.sx` et `GPU/@Device.sx` sont donc incompatibles. Les
racines portable, plateforme et cible exacte peuvent néanmoins fournir leurs
fragments correspondants selon les règles de
[composition ciblée](Fragments.md).

## Créer un module enfant

Sans préfixe `@`, le nom du fichier reste un segment de module :

```text
STD/Module/Math/@Scalar.sx         → STD.Math
STD/Module/Math/Geometry.sx        → STD.Math.Geometry
STD/Module/Math/Geometry/@Shape.sx → STD.Math.Geometry
```

Un module et ses enfants forment un espace qualifié. Une déclaration publique
explicitement nommée comme un enfant l'emporte sur ce module enfant ; une
déclaration privée ne le masque pas pour les appelants.

Sans `Package.json`, compiler ou éditer directement un atome utilise le parent
de son dossier comme racine implicite. `Sandbox/Test/@Display.sx` conserve
ainsi l'identité de module `Test`, et non `Test.@Display`.

[Revenir aux modules](README.md) ·
[Exposer ou masquer une déclaration](Visibility.md)
