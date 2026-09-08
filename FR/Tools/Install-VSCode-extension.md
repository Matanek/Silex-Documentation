# Installer l'extension Silex pour VS Code

L'extension Silex pour Visual Studio Code reconnaît les fichiers `.sx` et
fournit la coloration syntaxique, les paires de caractères, l'indentation et
les commentaires de ligne.

Pour Silex 0.44, elle n'est pas encore publiée dans Visual Studio Marketplace.
Construisez son paquet VSIX depuis le dépôt officiel, puis installez ce fichier
dans VS Code.

## Construire le fichier VSIX

Installez Git, Node.js et npm, puis clonez le dépôt :

```sh
git clone https://github.com/Matanek/Silex-Extension-VSCode.git
cd Silex-Extension-VSCode
npm ci
npm run package -- --out silex-language-support.vsix
```

La dernière commande crée `silex-language-support.vsix` dans le dossier du
dépôt.

## Installer l'extension

Si la commande `code` est accessible dans votre terminal, lancez :

```sh
code --install-extension silex-language-support.vsix
```

Sinon, ouvrez la palette de commandes de VS Code, lancez
`Extensions: Install from VSIX...`, puis sélectionnez le fichier créé.

Ouvrez ensuite un fichier `.sx` et vérifiez que VS Code affiche `Silex` comme
langage actif.

Cette version de l'extension est déclarative : elle ne lance pas encore le LSP
et n'offre donc ni diagnostics sémantiques, ni complétion, ni navigation vers
les définitions.

## Mettre l'extension à jour

Récupérez les sources, reconstruisez le même fichier puis forcez son
remplacement :

```sh
cd Silex-Extension-VSCode
git pull --ff-only
npm ci
npm run package -- --out silex-language-support.vsix
code --install-extension silex-language-support.vsix --force
```

Consultez le [dépôt de l'extension](https://github.com/Matanek/Silex-Extension-VSCode)
et la [procédure officielle d'installation d'un VSIX](https://code.visualstudio.com/docs/configure/extensions/extension-marketplace#_install-from-a-vsix).

[Revenir aux outils](README.md) · [Installer l'extension pour Zed](Install-Zed-extension.md)
