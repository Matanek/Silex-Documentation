# Installer Silex

Silex 0.44 est distribué sous forme d'un exécutable autonome. Son utilisation
ne demande ni Zig, ni Git, ni copie locale du dépôt.

Les distributions publiées prennent en charge les six hôtes desktop :

| Système | ARM64 | x64 |
| --- | --- | --- |
| macOS | `macos-arm64` | `macos-x64` |
| Linux | `linux-arm64` | `linux-x64` |
| Windows | `windows-arm64` | `windows-x64` |

Le script choisit l'archive de l'architecture native. Sous Windows ARM64, il
continue donc d'installer Silex ARM64 même lorsqu'il est lancé depuis un
PowerShell x64 exécuté par la couche de compatibilité du système.

## Installer sur macOS ou Linux

Vous pouvez [examiner le script officiel](https://github.com/Matanek/Silex/blob/v0.44.0/install.sh)
avant de l'exécuter :

```sh
curl -fsSL https://raw.githubusercontent.com/Matanek/Silex/v0.44.0/install.sh \
    | SILEX_VERSION=0.44.0 sh
```

Le script distingue macOS de Linux, puis ARM64 de x64, vérifie la somme
SHA-256 de l'archive correspondante et installe `silex` dans `~/.local/bin`.

### Rendre la commande accessible

Le script d'installation ne modifie pas la configuration de votre shell. Si
le terminal ne trouve pas immédiatement `silex`, ajoutez le dossier
d'installation au `PATH` de la session courante :

```sh
export PATH="$HOME/.local/bin:$PATH"
```

Si vous avez défini `SILEX_INSTALL_DIR`, remplacez `~/.local/bin` par le
dossier choisi.

Pour conserver ce réglage dans les prochains terminaux, ajoutez cette même
ligne au fichier de configuration de votre shell :

- `~/.zshrc` avec Zsh, notamment sur macOS ;
- `~/.bashrc` avec Bash, courant sur Linux.

Avec Zsh, rechargez ensuite le fichier sans fermer le terminal :

```sh
source ~/.zshrc
```

Avec Bash :

```sh
source ~/.bashrc
```

Sur certaines distributions Linux, `~/.profile` ajoute déjà
`~/.local/bin` au `PATH`. Dans ce cas, `source ~/.profile` suffit après la
première installation.

## Installer sur Windows

Exécutez cette commande dans PowerShell :

```powershell
$env:SILEX_VERSION = "0.44.0"
irm https://raw.githubusercontent.com/Matanek/Silex/v0.44.0/install.ps1 | iex
```

Le script vérifie la somme SHA-256 et installe `silex.exe` dans
`%LOCALAPPDATA%\Silex\bin`. L'exécutable Windows n'est pas encore signé :
Windows peut donc afficher un avertissement de réputation lors de sa première
utilisation.

Le script ne modifie pas non plus le `PATH` sous Windows. Si PowerShell ne
trouve pas `silex`, rendez la commande accessible dans la session courante :

```powershell
$env:PATH = "$env:LOCALAPPDATA\Silex\bin;$env:PATH"
```

Pour conserver ce réglage, ajoutez `%LOCALAPPDATA%\Silex\bin` au `Path` de
votre compte dans les variables d'environnement Windows, puis ouvrez un
nouveau terminal.

## Vérifier l'installation

Dans le terminal dont vous venez de recharger la configuration, ou dans un
nouveau terminal, lancez :

```sh
silex --version
```

Pour cette documentation, la commande doit afficher :

```text
silex 0.44.0
```

Préparez ensuite les outils vérifiés de la chaîne de compilation :

```sh
silex setup
```

Cette étape installe notamment LLVM 21.1.8 sur macOS 14 ou ultérieur avec une
machine ARM64, où LLVM est le backend par défaut. Elle ne demande pas
d'installation système de LLVM ou de Zig. Sur les autres hôtes, Silex conserve
actuellement son backend natif par défaut.

Vous pouvez maintenant [écrire et exécuter votre premier programme](../Learn/First-program.md).

## Choisir le dossier d'installation

Sur macOS ou Linux, placez le binaire dans un autre dossier avec
`SILEX_INSTALL_DIR` :

```sh
curl -fsSL https://raw.githubusercontent.com/Matanek/Silex/v0.44.0/install.sh \
    | SILEX_VERSION=0.44.0 SILEX_INSTALL_DIR="$HOME/bin" sh
```

Dans PowerShell :

```powershell
$env:SILEX_VERSION = "0.44.0"
$env:SILEX_INSTALL_DIR = "$HOME\bin"
irm https://raw.githubusercontent.com/Matanek/Silex/v0.44.0/install.ps1 | iex
```

[Revenir aux outils Silex](README.md) ·
[Mettre à jour et entretenir Silex](Maintain-Silex.md)
