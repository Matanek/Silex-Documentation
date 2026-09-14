# Écrire et exécuter un premier programme

Ce premier programme affiche un message dans le terminal. Vous avez seulement
besoin d'une [installation fonctionnelle de Silex](../Tools/Installation.md) et
d'un éditeur de texte.

## Créer le fichier source

Créez un dossier pour le programme, puis ajoutez-y un fichier `Main.sx` :

```sx
func main() {
    print("Bonjour, Silex !")
}
```

Une source Silex porte l'extension `.sx`. La fonction `main` est le point de
départ du programme. Ici, elle appelle `print` pour écrire une ligne dans le
terminal.

## Exécuter le programme

Si le terminal se trouve dans le dossier qui contient `Main.sx`, lancez :

```sh
silex run
```

Le terminal peut aussi se trouver dans un autre dossier. Par exemple, depuis
le dossier parent de `Project/` :

```sh
silex run Project
```

`silex run` cherche dans le dossier indiqué — ou dans le dossier courant si
vous omettez le chemin — l'unique fichier `.sx` qui déclare une fonction
`main` de premier niveau. Le fichier peut porter un autre nom que `Main.sx`.
La recherche reste limitée au dossier direct et ne descend pas dans ses
sous-dossiers.

Vous pouvez aussi indiquer le fichier explicitement :

```sh
silex run Project/Main.sx
```

Cette forme est nécessaire lorsque plusieurs fichiers du même dossier
déclarent `main`.

Le programme affiche :

```text
Bonjour, Silex !
```

`silex run` construit un exécutable, le lance et transmet son code de sortie au
terminal. Pour ce premier programme, aucune configuration de projet n'est
nécessaire. Le [guide de compilation](../Tools/Run-and-compile.md#choisir-le-backend-de-compilation)
explique comment sélectionner LLVM ou le backend Silex natif.

## Comprendre le dossier `.silex`

Silex crée un dossier `.silex` dans le dossier depuis lequel vous lancez la
commande. Il y conserve notamment son cache de compilation et l'exécutable
privé utilisé par `silex run`.

Pour ne pas disperser plusieurs dossiers `.silex`, lancez vos commandes depuis
une racine de projet stable et transmettez le chemin de l'application ou de la
source, comme dans `silex run Project` ci-dessus.

Si votre projet utilise Git, ignorez les fichiers générés par Silex en ajoutant
cette ligne à son fichier `.gitignore` :

```text
.silex/
```

[Revenir au parcours d'apprentissage](README.md) ·
[Comprendre les variables](../Language/Values/Variables.md)
