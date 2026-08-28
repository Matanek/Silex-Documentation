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

[Revenir au parcours d'apprentissage](README.md) ·
[Leçon suivante : conserver et modifier des valeurs](Variables.md)
