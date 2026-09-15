# Consulter la référence de la CLI

Cette page rassemble les commandes et options acceptées par Silex 0.44. Les
guides liés expliquent quand les employer et ce qu'elles produisent.

## Programmes et tests

```text
silex run [source.sx|directory] [--backend <native|llvm>]
    [-d|--debug|-r|--release] [-n|--nocache] [--emit-ir]
silex interpret <source.sx> [-n|--nocache] [--emit-ir]
silex test <source.sx|directory> [--backend <native|llvm>]
    [-n|--nocache] [--emit-ir]
silex compile <source.sx> [--backend <native|llvm>] [--target <target>]
    [-d|--debug|-r|--release] [-n|--nocache]
    -o|--output <executable>
```

| Option | Commandes | Effet |
| --- | --- | --- |
| `--backend` | `run`, `test`, `compile` | choisit explicitement `native` ou `llvm` |
| `-d`, `--debug` | `run`, `compile` | choisit Debug (défaut de `run`) et désactive les optimisations Release |
| `-r`, `--release` | `run`, `compile` | choisit Release (défaut de `compile`) |
| `-n`, `--nocache` | `run`, `interpret`, `test`, `compile` | ignore le cache pour cette commande |
| `--emit-ir` | `run`, `interpret`, `test` | écrit l'IR textuelle avant le résultat |
| `-o`, `--output` | `compile` | choisit le chemin obligatoire de l'exécutable |
| `--target` | `compile` | choisit une cible reconnue au lieu de l'hôte |

`run` conserve un fichier `.sx` explicite comme point d'entrée. Avec un
dossier, ou sans chemin pour le dossier courant, il choisit l'unique fichier
`.sx` directement contenu dans ce dossier qui déclare une fonction `main` de
premier niveau. Il ne parcourt pas les sous-dossiers. Si aucun fichier ou
plusieurs fichiers conviennent, la commande échoue et demande un chemin de
source explicite.

Sans `--backend`, macOS ARM64 choisit LLVM ; les autres hôtes distribués
choisissent le backend natif. LLVM est actuellement qualifié uniquement sur
`macos-arm64`. Une sélection explicite qui n'est pas disponible échoue sans
exécuter l'autre backend. `interpret` n'est pas un backend de compilation et
refuse donc cette option.

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

## Connexion au registre en qualification

Le candidat de publication directe ajoute `silex login [--no-browser]` et
`silex logout`. Ces commandes ne sont pas encore livrées ni déployées ; leur
périmètre et leurs limites figurent dans
[Se connecter au registre avec GitHub](Registry-login.md).

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
