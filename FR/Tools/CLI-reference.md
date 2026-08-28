# Consulter la référence de la CLI

Cette page rassemble les commandes et options acceptées par Silex 0.42. Les
guides liés expliquent quand les employer et ce qu'elles produisent.

## Programmes et tests

```text
silex run <source.sx> [-d|--debug|-r|--release] [-n|--nocache] [--emit-ir]
silex interpret <source.sx> [-n|--nocache] [--emit-ir]
silex test <source.sx|directory> [-n|--nocache] [--emit-ir]
silex compile <source.sx> [--target <target>]
    [-d|--debug|-r|--release] [-n|--nocache]
    -o|--output <executable>
```

| Option | Commandes | Effet |
| --- | --- | --- |
| `-d`, `--debug` | `run`, `compile` | désactive les optimisations Release pour le diagnostic natif |
| `-r`, `--release` | `run`, `compile` | sélectionne explicitement le mode par défaut |
| `-n`, `--nocache` | `run`, `interpret`, `test`, `compile` | ignore le cache pour cette commande |
| `--emit-ir` | `run`, `interpret`, `test` | écrit l'IR textuelle avant le résultat |
| `-o`, `--output` | `compile` | choisit le chemin obligatoire de l'exécutable |
| `--target` | `compile` | choisit une cible reconnue au lieu de l'hôte |

Consultez [Exécuter, interpréter ou compiler un programme](Run-and-compile.md)
et [Écrire et lancer des tests](Tests.md).

## Installation et packages

```text
silex install <package|package-directory> [--suite] [--dev]
    [--target <target>]
silex check <package-directory>
silex register <package-directory>
silex link <package-directory> [--workspace <directory>]
    [--target <target>]
silex unlink <package-name> [--workspace <directory>]
silex packages
silex packages resolve [source.sx|project-directory]
```

| Option | Commandes | Effet |
| --- | --- | --- |
| `--suite` | `install` | installe les extensions de suite compatibles d'un package enregistré |
| `--dev` | `install` | ajoute les dépendances de développement du package demandé |
| `--target` | `install`, `link` | sélectionne la cible utilisée pour préparer le package |
| `--workspace` | `link`, `unlink` | limite le lien au dossier de travail indiqué |

`packages` inventorie les versions installées et les liens utilisateur.
`packages resolve` affiche le graphe effectivement choisi pour le chemin
indiqué ; sans chemin, il analyse le dossier courant.

Consultez [Installer et choisir des packages](Install-packages.md),
[Développer avec des packages locaux](Develop-packages.md) et
[Publier un package](Publish-package.md).

## Compilateur et éditeurs

```text
silex setup
silex update
silex targets
silex version
silex lsp
```

`setup`, `update`, `targets`, `version` et `lsp` n'acceptent aucun argument.
`silex lsp` parle le protocole LSP sur les flux standards ; il est normalement
lancé par une extension d'éditeur, pas directement dans un terminal interactif.

Les formes générales suivantes sont également disponibles :

```text
silex
silex --help
silex --version
silex -V
```

Sans commande, Silex affiche la même aide que `--help`.

Consultez [Mettre à jour et entretenir Silex](Maintain-Silex.md) et les pages
d'installation pour [Zed](Install-Zed-extension.md) ou
[VS Code](Install-VSCode-extension.md).

[Revenir aux outils](README.md)
