# Développer avec des packages locaux

Un lien de développement demande à Silex de lire directement les sources d'un
package local. Les modifications du package sont alors visibles dès la
prochaine compilation, sans nouvelle installation.

Depuis une racine de travail stable, liez le dossier du package :

```sh
silex link UserPackages/MonPackage
```

La commande lit et valide son `Package.json`, prépare les éventuels artefacts
de la cible actuelle puis enregistre un lien pour l'utilisateur. Le nom utilisé
ensuite par les manifestes et les imports reste celui déclaré par le package,
pas le chemin du dossier.

La forme complète du manifeste est détaillée dans
[Définir un package avec `Package.json`](Package-manifest.md).

## Limiter le lien à un workspace

Un lien utilisateur influence tous les projets compatibles. Pour tester un
checkout dans un seul espace de travail, indiquez sa racine :

```sh
silex link UserPackages/MonPackage --workspace Project
```

Silex conserve ce lien sous `Project/.silex/links`. Lorsqu'il résout une source,
il recherche le dossier `.silex/links` le plus proche parmi le projet et ses
parents.

Un lien de workspace l'emporte sur un lien utilisateur du même nom. Un lien
utilisateur l'emporte à son tour sur les versions installées. Dans chaque cas,
la version liée doit encore satisfaire la contrainte déclarée par le projet.

## Retirer un lien

Retirez un lien utilisateur avec le nom du package :

```sh
silex unlink MonPackage
```

Pour retirer le lien propre au workspace, répétez exactement sa portée :

```sh
silex unlink MonPackage --workspace Project
```

La suppression du lien ne supprime ni le dossier source ni les versions
installées. Au prochain calcul du graphe, Silex peut donc reprendre la version
installée compatible.

## Vérifier le package réellement choisi

Affichez les packages globaux connus avec :

```sh
silex packages
```

Puis inspectez la résolution d'un projet précis :

```sh
silex packages resolve Project/Main.sx
```

L'origine `workspace-link`, `user-link` ou `installed` indique immédiatement
pourquoi ce chemin a été sélectionné.

## Préparer une cible différente

Un package qui contient une frontière native peut demander une préparation
propre à la cible. Ajoutez alors son nom au moment de créer le lien :

```sh
silex link UserPackages/MonPackage --target linux-x64
```

Cette option ne rend pas le lien spécifique à une cible : elle sélectionne la
cible utilisée pour valider et préparer le package au moment de la commande.

Avant une publication, utilisez `silex check` pour valider le contrat
installable sans modifier le dépôt. Le parcours complet est décrit dans
[Publier un package dans le registre](Publish-package.md).

[Revenir aux outils](README.md) ·
[Installer et choisir des packages](Install-packages.md) ·
[Consulter la référence de la CLI](CLI-reference.md)
