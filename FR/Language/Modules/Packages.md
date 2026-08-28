# Comprendre les frontières d'un package

Une frontière de package détermine l'identité de ses modules, les packages
qu'ils peuvent voir et la façon dont un espace de noms peut être partagé. La
forme complète du fichier de projet est documentée dans
[Définir un package avec `Package.json`](../../Tools/Package-manifest.md).

## Faire correspondre sources et identité de module

Un manifeste choisit une racine physique de sources, `Module/` par défaut,
sans ajouter ce nom physique aux modules logiques. Avec
`"sources": "Sources"`, `Sources/Geometry/Vec3.sx` fournit
`Geometry.Vec3` dans une application sans nom et `Math.Geometry.Vec3` dans le
package `Math`.

Le chemin `sources` est relatif à `Package.json`. La valeur `"."` sélectionne
la racine du package. Un seul dossier est accepté : aucun chemin absolu, barre
oblique inverse, segment vide, `.` ou `..`, séparateur répété, séparateur final,
glob ou tableau n'est autorisé.

Dans cette racine, chaque fichier `@Nom.sx` contribue au module représenté par
son dossier. À la racine `Module/` d'un package `Math`, `@Operations.sx` et
`@Vectors.sx` appartiennent donc tous deux à `Math`. Leurs noms physiques ne
s'importent jamais. `@Module.sx` reste accepté comme ancien nom conventionnel,
sans sémantique particulière. Consultez la
[correspondance entre fichiers et modules](Layout.md) pour le découpage complet.

## Sélectionner les sources de la cible

Un package peut ajouter des racines pour la plateforme et la cible exacte :

```text
Platform/MacOS/Module/
Platform/Linux/Module/
Platform/Windows/Module/
Target/macos-arm64/Module/
Target/linux-x64/Module/
Target/windows-x64/Module/
Target/windows-arm64/Module/
```

Les segments physiques n'apparaissent jamais dans les noms de modules. Une
valeur `sources` personnalisée remplace le dernier dossier `Module` dans les
trois familles de racines. La racine de plateforme accueille le code commun à
ses architectures ; la cible exacte reste réservée aux dépendances réelles à
l'architecture ou à l'ABI. Les autres plateformes et cibles sont ignorées.

Plusieurs racines actives du même package peuvent contribuer au même module de
façon additive. Aucune ne remplace l'autre. Le code portable atteint les
déclarations homonymes spécialisées par `Platform.name` ou `Target.name`.

Chaque fragment peut contenir un `main` local pour une expérimentation directe.
Seul le `main` du fichier exact passé comme entrée est conservé ; tous les
autres sont ignorés et ne deviennent jamais membres du module.

Un fichier local placé hors des racines publiques peut être compilé comme
entrée explicite. Lui seul devient disponible pour cette compilation : son
dossier voisin n'est pas indexé et il ne devient pas un fragment supplémentaire
d'un module public existant.

## Déclarer chaque dépendance directe

Une application voit seulement les packages qu'elle déclare directement. Une
dépendance transitive n'est jamais automatiquement accessible. Un dossier
nommé `Packages/` n'a aucune signification spéciale et ne rend pas ses voisins
visibles.

Le manifeste déclare cette relation dans `dependencies`. Sa forme et la
distinction avec `devDependencies` sont décrites dans la
[documentation de `Package.json`](../../Tools/Package-manifest.md#déclarer-les-dépendances).

Pour une entrée explicite, Silex cherche le `Package.json` le plus proche dans
son dossier puis dans ses parents. La résolution ne dépend donc pas du dossier
depuis lequel la commande est lancée. Avec un manifeste, les dépendances
déclarées remplacent l'environnement implicite d'un programme libre.

`Package` et `Module` sont réservés comme racines contextuelles. Une identité de
package ne peut être exactement l'un de ces noms ni commencer par `Package.` ou
`Module.`.

## Autoriser un package enfant

Un nom qualifié étend l'espace de noms de chacun de ses préfixes. Le parent doit
autoriser explicitement un package enfant distribué séparément. `GFX.*`
autorise seulement les enfants directs tels que `GFX.UI`, jamais
`GFX.UI.Controls`.

Chaque autorisation exacte peut accorder trois permissions indépendantes :

- `friend` ouvre les déclarations `package` du parent à cet enfant ;
- `suite` rend cet enfant sélectionnable lors de l'installation explicite de
  la suite du parent ;
- `merge` ouvre le seul module principal exact de l'enfant à une composition
  publique additive avec celui du parent.

Ces permissions valent `false` par défaut. Un joker peut porter `friend: true`,
ce qui accorde volontairement l'accès à chaque futur enfant correspondant.
`suite` et `merge` sont refusés sur un joker. Si une entrée exacte et un joker
correspondent, l'entrée exacte définit toutes les permissions.

L'autorisation délègue un nom, jamais l'autorité du parent. Si le parent fournit
déjà le module exact `GFX.Physics`, il reste canonique. Sans `merge`, la présence
du module principal de l'enfant est une collision. Avec `merge`, seules les
déclarations publiques sont additionnées, chaque propriétaire reste connu et
toute collision publique est refusée. Les visibilités `module` et `package` ne
fusionnent pas ; `friend` reste une décision distincte.

La relation `friend` va de l'enfant nommé vers le parent déclarant. Elle
n'installe ni n'active l'enfant, ne remplace aucune dépendance, n'ouvre aucune
déclaration `module`, `local`, `private` ou `protected`, et ne rend rien public
aux consommateurs ordinaires. Une suite ne crée aucune dépendance du parent
vers l'enfant.

Les catalogues de façade ouverts aux contributions sont expliqués dans
[les réexportations](Reexports.md). La déclaration JSON des autorisations et
catalogues reste regroupée dans la
[documentation du manifeste](../../Tools/Package-manifest.md#autoriser-la-composition-entre-packages).

[Revenir aux modules](README.md) ·
[Composer des fragments ciblés](Fragments.md) ·
[Définir `Package.json`](../../Tools/Package-manifest.md)
