# Installer Silex

Silex 0.42 est distribué sous forme d'un exécutable autonome. Son utilisation
ne demande ni Zig, ni Git, ni copie locale du dépôt.

Les distributions publiées prennent en charge :

- macOS sur Apple Silicon ;
- Linux x64 ;
- Windows x64.

## Installer sur macOS ou Linux

Vous pouvez [examiner le script officiel](https://github.com/Matanek/Silex/blob/v0.42.0/install.sh)
avant de l'exécuter :

```sh
curl -fsSL https://raw.githubusercontent.com/Matanek/Silex/v0.42.0/install.sh \
    | SILEX_VERSION=0.42.0 sh
```

Le script sélectionne la distribution adaptée, vérifie sa somme SHA-256 et
installe `silex` dans `~/.local/bin`.

Si le terminal ne trouve pas ensuite la commande, ajoutez ce dossier à votre
variable `PATH` en suivant la méthode de votre shell.

## Installer sur Windows

Exécutez cette commande dans PowerShell :

```powershell
$env:SILEX_VERSION = "0.42.0"
irm https://raw.githubusercontent.com/Matanek/Silex/v0.42.0/install.ps1 | iex
```

Le script vérifie la somme SHA-256 et installe `silex.exe` dans
`%LOCALAPPDATA%\Silex\bin`. L'exécutable Windows n'est pas encore signé :
Windows peut donc afficher un avertissement de réputation lors de sa première
utilisation.

## Vérifier l'installation

Ouvrez un nouveau terminal si vous venez de modifier `PATH`, puis lancez :

```sh
silex --version
```

Pour cette documentation, la commande doit afficher :

```text
silex 0.42.0
```

Vous pouvez maintenant [écrire et exécuter votre premier programme](../Learn/First-program.md).

## Choisir le dossier d'installation

Sur macOS ou Linux, placez le binaire dans un autre dossier avec
`SILEX_INSTALL_DIR` :

```sh
curl -fsSL https://raw.githubusercontent.com/Matanek/Silex/v0.42.0/install.sh \
    | SILEX_VERSION=0.42.0 SILEX_INSTALL_DIR="$HOME/bin" sh
```

Dans PowerShell :

```powershell
$env:SILEX_VERSION = "0.42.0"
$env:SILEX_INSTALL_DIR = "$HOME\bin"
irm https://raw.githubusercontent.com/Matanek/Silex/v0.42.0/install.ps1 | iex
```

## Mettre Silex à jour

Une installation existante peut être remplacée par la dernière version
publiée :

```sh
silex update
```

La commande conserve le dossier du binaire actuel et vérifie la somme SHA-256
avant de le remplacer.

## Préparer les outils complémentaires

Les programmes Silex ordinaires fonctionnent immédiatement. Avant de compiler
des shaders HLSL ou d'utiliser une frontière native qui le demande, installez
une fois les outils vérifiés de la chaîne Silex :

```sh
silex setup
```

Ces outils sont installés dans le dossier utilisateur `.silex`. Ils restent
des détails de la chaîne de compilation et ne deviennent pas des dépendances de
votre application.

[Revenir aux outils Silex](README.md)
