# Conserver et modifier des valeurs

Un programme donne un nom aux valeurs qu'il utilise. Certaines ne changeront
pas ; d'autres doivent évoluer.

## Déclarer les valeurs

Créez un fichier `Variables.sx` :

```sx
func main() {
    let language:str = "Silex"
    var lessons = 1

    lessons = 2

    print(language)
    print(lessons)
}
```

`language` conserve la même valeur. `lessons` commence à `1`, puis reçoit la
valeur `2`.

## Exécuter le programme

Indiquez le chemin de la source à `silex run` :

```sh
silex run Variables.sx
```

Le programme affiche :

```text
Silex
2
```

## Choisir entre `let` et `var`

Utilisez `let` lorsque le nom gardera la valeur reçue à sa déclaration. Une
nouvelle affectation à ce nom sera refusée par le compilateur.

Utilisez `var` lorsque le programme doit lui attribuer une autre valeur. Ici,
la ligne `lessons = 2` exige donc que `lessons` soit déclaré avec `var`.

Commencez par `let` et passez à `var` seulement lorsqu'une modification est
nécessaire. L'intention de votre code reste ainsi visible dès la déclaration.

## Indiquer ou laisser déduire le type

Une annotation placée après le nom fixe explicitement le type, comme dans
`let language:str = "Silex"`.

Sans annotation, le compilateur déduit le type depuis la première valeur.
Dans l'exemple, il déduit que `lessons` est un `int` à partir de `1`.

[Leçon précédente : écrire un premier programme](First-program.md) ·
[Revenir au parcours d'apprentissage](README.md)
