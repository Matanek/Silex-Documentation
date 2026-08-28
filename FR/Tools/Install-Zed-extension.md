# Installer l'extension Silex pour Zed

L'extension Silex pour Zed fournit la coloration syntaxique, les diagnostics,
la complétion et la navigation grâce au serveur lancé par `silex lsp`.

Pour Silex 0.42, l'extension n'est pas encore disponible dans la galerie Zed.
Installez-la comme extension de développement depuis son dépôt officiel.

## Préparer les outils nécessaires

[Installez Silex](Installation.md), puis vérifiez que sa commande est
accessible :

```sh
silex --version
```

Installez aussi Git et Rust avec `rustup`, puis préparez la cible utilisée par
les extensions Zed :

```sh
rustup target add wasm32-wasip2
```

Si Silex vient d'être ajouté au `PATH` pendant que Zed était ouvert, redémarrez
Zed afin que l'extension puisse trouver la commande.

## Installer le dépôt dans Zed

Clonez l'extension dans un dossier que vous conserverez :

```sh
git clone https://github.com/Matanek/Silex-Extension-Zed.git
```

Dans Zed :

1. ouvrez la palette de commandes ;
2. lancez `zed: install dev extension` ;
3. sélectionnez le dossier `Silex-Extension-Zed` qui contient
   `extension.toml`.

Ouvrez ensuite un fichier `.sx`. Zed doit sélectionner le langage Silex et
démarrer automatiquement `silex lsp`.

## Mettre l'extension à jour

Récupérez la nouvelle version du dépôt :

```sh
git -C Silex-Extension-Zed pull --ff-only
```

Dans la palette de commandes de Zed, lancez ensuite
`zed: rebuild dev extension`. Si seule la commande `silex` a été mise à jour,
`editor: restart language server` suffit.

En cas d'échec du serveur ou de la compilation de l'extension, ouvrez le
journal avec `zed: open log` et vérifiez à nouveau `silex --version` dans un
terminal.

Consultez le [dépôt de l'extension](https://github.com/Matanek/Silex-Extension-Zed)
et la [procédure officielle des extensions de développement Zed](https://zed.dev/docs/extensions/developing-extensions#developing-an-extension-locally).

[Revenir aux outils](README.md) · [Installer l'extension pour VS Code](Install-VSCode-extension.md)
