# Installer et choisir des packages

Installez un package public avec son nom enregistré :

```sh
silex install STD
```

Depuis la bascule du domaine public, Silex 0.45.0 reconnaît la capacité `/v2`
du registre Cloudflare et lit les versions conservées dans D1/R2. Une panne de
cette vérification n'entraîne pas de retour silencieux vers l'ancien index
`/v1`.

Le client compatible lit les sources et artefacts conservés par le registre
Cloudflare, sans connexion GitHub ni accès au dépôt de l'auteur. Silex
sélectionne la version publiée la plus récente qui reste compatible avec
la version du compilateur. Pour imposer une version précise, ajoutez-la après
`@` :

```sh
silex install STD@0.19.0
```

Les dépendances déclarées par cette version sont résolues et installées
automatiquement. L'installation ne modifie jamais le `Package.json` de votre
application.

## Déclarer le package utilisé par un projet

Une application qui possède un manifeste doit déclarer directement chaque
package qu'elle importe :

```json
{
  "dependencies": {
    "STD": "^0.19.0"
  }
}
```

`^0.19.0` accepte une version publiée égale ou supérieure à `0.19.0`, sans
franchir la prochaine version majeure. Utilisez `=0.19.0` lorsque le projet
demande exactement cette version.

Une dépendance transitive n'est pas visible automatiquement dans votre code.
La page [Définir un package avec `Package.json`](Package-manifest.md) explique
le manifeste et les contraintes de version. Les effets sur la visibilité et
l'identité des modules sont décrits dans
[les frontières de package](../Language/Modules/Packages.md).

## Installer une copie locale

Une commande peut aussi installer l'état actuel d'un dossier de package :

```sh
silex install UserPackages/MonPackage
```

Cette opération copie une version dans le magasin utilisateur. Les modifications
ultérieures du dossier source ne sont donc pas prises en compte. Pour travailler
directement sur ses fichiers, utilisez plutôt un
[lien de développement](Develop-packages.md).

## Préparer les dépendances de développement

`--dev` ajoute les `devDependencies` du package explicitement demandé :

```sh
silex install MonPackage --dev
```

Leurs dépendances ordinaires sont installées, mais leurs propres
`devDependencies` ne sont pas installées récursivement. Sans `--dev`, ces
outils de test, d'exemple ou de benchmark restent absents du graphe consommé
par les utilisateurs du package.

## Installer une suite officielle

Certains packages déclarent des extensions publiées séparément comme membres
de leur suite. Installez le package et les membres compatibles avec :

```sh
silex install GFX --suite
```

L'installation est étendue à la suite uniquement pour un nom provenant du
registre. Cette option n'est pas disponible lorsque l'opérande est un dossier
local, et une dépendance transitive ne la déclenche jamais implicitement.

`--suite` et `--dev` répondent à deux intentions indépendantes et peuvent être
combinés.

## Préparer une autre cible

L'installation sélectionne la cible de la machine actuelle par défaut. Lorsqu'un
package fournit des artefacts propres à une autre cible, précisez-la :

```sh
silex install MonPackage --target windows-x64
```

Les cibles reconnues sont affichées par `silex targets`.

## Voir les packages disponibles et sélectionnés

Affichez les versions installées et les liens valables pour tout l'utilisateur :

```sh
silex packages
```

Chaque ligne donne le nom, la version, l'origine et le chemin. Les origines
possibles sont notamment `installed` et `user-link`.

Pour observer le graphe réellement sélectionné pour une source ou un projet,
utilisez :

```sh
silex packages resolve Project/Main.sx
silex packages resolve Project
```

Cette seconde commande tient compte du manifeste le plus proche, des versions,
des liens de développement et des dépendances de développement du projet.

## Retirer une version installée

Silex 0.44 ne possède pas de commande de désinstallation de package. La sortie
de `silex packages` donne le chemin exact de chaque version. Supprimez seulement
le dossier `Name@MAJOR.MINOR.PATCH` concerné sous `~/.silex/packages`, ou sous
`%USERPROFILE%\.silex\packages` avec Windows.

Cette suppression ne modifie aucun manifeste. Un projet qui demande cette
version devra donc installer une autre version compatible avant de pouvoir
être résolu à nouveau.

[Revenir aux outils](README.md) ·
[Développer avec des packages locaux](Develop-packages.md) ·
[Publier un package](Publish-package.md)
