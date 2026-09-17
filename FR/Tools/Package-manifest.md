# Définir un package avec `Package.json`

`Package.json` se place à la racine d'une application ou d'un package. Il
décrit les sources du projet, les packages nécessaires et, pour un package
publié, son identité et ses fichiers natifs éventuels.

| Intention | Champs |
| --- | --- |
| Identifier et présenter le package | `name`, `version`, `description`, `authors`, `repository`, `requires` |
| Choisir les sources | `sources` |
| Construire le graphe de packages | `dependencies`, `devDependencies` |
| Partager un espace de noms | `extensions`, `catalogs` |
| Préparer une intégration native | `boundary`, `artifacts` |

Une application peut se contenter de `sources` et de ses dépendances. Les
champs qui accordent un espace de noms, ouvrent des catalogues ou déclarent une
frontière native appartiennent à un package nommé.

## Identifier et présenter le package

Un package destiné à être partagé déclare au minimum son nom, sa version et sa
compatibilité avec Silex :

```json
{
  "name": "MonPackage",
  "version": "1.0.0",
  "description": {
    "en": "Reusable utilities for Silex applications.",
    "fr": "Outils réutilisables pour les applications Silex."
  },
  "authors": ["Matanek"],
  "requires": {
    "silex": ">=0.44.0"
  }
}
```

`name` est l'identité employée dans les dépendances et les imports. Le dossier
d'un package local porte le même nom. `version` suit la forme
`MAJOR.MINOR.PATCH`. Une fois cette version publiée, ses octets ne peuvent
pas être remplacés.

`requires.silex` commence par une borne minimale inclusive. Privilégiez une
plage ouverte telle que `">=0.44.0"` : elle autorise l'utilisation du package
avec les versions suivantes de Silex tant qu'aucune incompatibilité n'est
connue. Elle évite notamment de bloquer chaque nouvelle version mineure par
précaution.

Une borne maximale exclusive existe, par exemple
`">=0.44.0 <0.45.0"`. Cette plage bornée est moins courante : réservez-la à une
incompatibilité connue ou à un contrat qui doit réellement s'arrêter avant
cette version. Un package installé doit déclarer sa compatibilité ; un package
local en cours de développement peut encore omettre `requires.silex`.

### Décrire le package dans une ou plusieurs langues

Une chaîne simple s'applique à toutes les langues :

```json
{
  "description": "Reusable utilities for Silex applications."
}
```

Un objet localisé associe chaque balise de langue à sa description, comme dans
le premier exemple. Il doit contenir `en`. Silex cherche successivement la
langue exacte, sa langue principale — `fr` pour `fr-FR` — puis `en`.

Chaque texte doit être une ligne non vide, sans espaces superflus au début ou
à la fin. Les balises sont comparées sans tenir compte de la casse et ne
peuvent pas être répétées.

`authors` est un tableau facultatif de noms non vides et uniques. Leur ordre
est conservé. Ce champ attribue le travail ; il n'accorde aucun droit sur le
registre, l'espace de noms ou les sources.

`repository` est une adresse GitHub HTTPS facultative vers le développement du
package. Elle aide les contributeurs à trouver le dépôt. Le registre conserve
les fichiers transmis depuis le dossier local ; cette adresse ne sert ni à les
obtenir, ni à prouver la propriété d'un nom.

## Choisir les sources

`sources` choisit le dossier de sources portable, relativement au manifeste :

```json
{
  "sources": "Sources"
}
```

La valeur par défaut est `Module`. La valeur `"."` sélectionne la racine du
projet. Un manifeste accepte un seul dossier, sans chemin absolu, barre oblique
inverse, glob, tableau, segment vide, `.` ou `..` interne.

La même valeur est reprise sous les racines `Platform/<OS>/` et
`Target/<target>/`. La correspondance entre ces fichiers physiques et leurs
modules logiques est expliquée dans
[les frontières de package du langage](../Language/Modules/Packages.md).

## Déclarer les dépendances

`dependencies` contient les packages nécessaires au code distribué.
`devDependencies` contient seulement les outils utilisés pour développer,
tester, illustrer ou mesurer le package :

```json
{
  "dependencies": {
    "STD": "^0.20.0"
  },
  "devDependencies": {
    "GFX.Viewer": "^0.3.0"
  }
}
```

`^1.4.0` accepte la version demandée et les versions plus récentes du même
numéro majeur. `=1.4.0` sélectionne exactement cette version. Cette règle
s'applique aussi pendant la série `0.x` : `^0.7.0` accepte `0.8.0`, mais pas
`1.0.0`.

Un package ne peut pas figurer dans les deux objets. Les consommateurs voient
seulement `dependencies` et doivent encore déclarer directement chaque package
qu'ils importent. Les `devDependencies` rejoignent uniquement le graphe de
développement du package racine ; elles ne se propagent pas récursivement.

La commande qui prépare ce graphe est documentée dans
[Installer et choisir des packages](Install-packages.md#préparer-les-dépendances-de-développement).

## Autoriser la composition entre packages

`extensions` autorise des packages enfants distribués séparément. `catalogs`
ouvre des modules de façade aux blocs `contribute`. Voici l'extrait pertinent
du manifeste de GFX :

```json
{
  "extensions": {
    "GFX.Physics": {
      "friend": true
    },
    "GFX.UI": {
      "suite": true
    },
    "GFX.GPU": {
      "friend": true,
      "suite": true
    }
  },
  "catalogs": ["GFX.Components", "GFX.Plugins", "GFX.Resources"]
}
```

Une entrée vide telle que `"GFX.UI": {}` autorise seulement l'identité de
l'enfant. Une entrée exacte peut ajouter trois permissions indépendantes :

- `friend` ouvre les déclarations `package` du parent à l'enfant ;
- `suite` rend l'enfant sélectionnable avec l'installation `--suite` du parent ;
- `merge` autorise une composition publique additive du module principal exact.

Le joker `Parent.*` couvre seulement les enfants directs. Il peut porter
`friend`, mais pas `suite` ni `merge`.

`catalogs` est indépendant de ces permissions : lui seul ouvre les modules
nommés aux contributions. Tout package nommé peut contribuer, qu'il soit enfant
ou externe, à condition de déclarer le propriétaire du catalogue comme
dépendance directe. Cette participation n'accorde aucun droit `friend`,
`suite`, `merge` ni aucune portion de son espace de noms. Les effets sur la
visibilité et la composition sont détaillés dans
[les frontières de package](../Language/Modules/Packages.md) et
[les réexportations](../Language/Modules/Reexports.md).

## Déclarer une frontière native

`boundary` décrit les entrées natives privées que le compilateur peut lier pour
une cible. Chaque branche contient des fournisseurs nommés :

```json
{
  "boundary": {
    "macos-arm64": {
      "providers": {
        "Native": {
          "archive": "Boundary/macos-arm64/libNative.a",
          "frameworks": ["CoreFoundation"]
        }
      }
    },
    "linux-x64": {
      "providers": {
        "Native": {
          "archive": "Boundary/linux-x64/libNative.a",
          "libraries": ["m", "pthread"]
        }
      }
    }
  }
}
```

| Champ du fournisseur | Rôle |
| --- | --- |
| `archive` | Archive statique relative au package et compatible avec la cible |
| `frameworks` | Frameworks Apple nommés, uniquement sous macOS |
| `libraries` | Bibliothèques système nommées, sans chemin ni option brute du linker |
| `requires` | Autres fournisseurs sous la forme `Package.Provider` |

Un fournisseur déclare au moins une de ces entrées. Il peut se limiter à une
bibliothèque système, un framework ou un autre fournisseur et n'a alors besoin
d'aucune archive factice. Une exigence peut viser le package lui-même ou une
dépendance directe, par exemple `"requires": ["GFX.SDL3"]`.

Silex sélectionne seulement la branche de la cible active. Lorsqu'une archive
est déclarée, son format et son architecture doivent correspondre à cette
cible. La frontière reste privée au package : ses consommateurs voient son API
Silex publique, pas les archives, frameworks, bibliothèques ou symboles
étrangers. La source qui appelle un fournisseur est documentée dans
[Relier un package à une API système](../Language/Interop/README.md).

## Préparer des artefacts vérifiés

`artifacts` décrit les gros fichiers nécessaires par cible. Une archive
utilisée par `boundary` peut ainsi être préparée au chemin attendu :

```json
{
  "artifacts": {
    "macos-arm64": {
      "Native": {
        "path": "Boundary/macos-arm64/libNative.a",
        "url": "https://example.com/releases/libNative.a",
        "sha256": "0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef"
      }
    }
  }
}
```

`path` reste à l'intérieur du package et `sha256` contient les 64 chiffres
hexadécimaux de l'empreinte attendue. `url` est facultatif ; s'il est présent,
il utilise HTTPS et permet à un package local de récupérer un fichier manquant
pendant sa préparation. `silex publish` exige que le fichier déclaré soit
présent et conforme à son empreinte, puis l'envoie comme objet distinct au
registre. `silex install` d'une version publiée lit cet objet dans le registre,
sans dépendre de l'URL de développement. Une compilation ne télécharge jamais
de fichier.

`artifacts` prépare donc un fichier, tandis que `boundary` décide comment ce
fichier participe à la liaison native. Les deux champs sont indépendants : une
petite archive déjà incluse parmi les sources n'a pas besoin d'entrée dans
`artifacts`.

## Valider le manifeste

Avant de publier un package, validez son identité, sa version et le contrat de
sa cible actuelle :

```sh
silex check MonPackage
```

Pour préparer ses artefacts sur une autre cible, utilisez ensuite `--target`
avec `silex install` ou `silex link`. Consultez
[Développer avec des packages locaux](Develop-packages.md) et
[Publier un package dans le registre](Publish-package.md) pour les parcours
complets.

[Revenir aux outils](README.md) ·
[Installer et choisir des packages](Install-packages.md) ·
[Comprendre les frontières de package](../Language/Modules/Packages.md)
