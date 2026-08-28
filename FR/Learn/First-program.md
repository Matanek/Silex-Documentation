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

Indiquez à `silex run` le chemin du fichier source. Si le terminal se trouve
dans le dossier qui contient `Main.sx`, lancez :

```sh
silex run Main.sx
```

Le terminal peut aussi se trouver dans un autre dossier. Par exemple, depuis
le dossier parent de `Project/` :

```sh
silex run Project/Main.sx
```

Le programme affiche :

```text
Bonjour, Silex !
```

`silex run` construit un exécutable natif, le lance et transmet son code de
sortie au terminal. Pour ce premier programme, aucune configuration de projet
n'est nécessaire.

## Comprendre le dossier `.silex`

Silex crée un dossier `.silex` dans le dossier depuis lequel vous lancez la
commande. Il y conserve notamment son cache de compilation et l'exécutable
privé utilisé par `silex run`.

Pour ne pas disperser plusieurs dossiers `.silex`, lancez vos commandes depuis
une racine de projet stable et transmettez le chemin de la source, comme dans
`silex run Project/Main.sx` ci-dessus.

Si votre projet utilise Git, ignorez les fichiers générés par Silex en ajoutant
cette ligne à son fichier `.gitignore` :

```text
.silex/
```

[Revenir au parcours d'apprentissage](README.md) ·
[Comprendre les variables](../Language/Variables.md)
