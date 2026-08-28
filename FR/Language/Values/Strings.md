# Construire du texte

Une valeur `str` contient du texte UTF-8 immuable. Vous pouvez assembler des
chaînes avec `+` ou injecter directement une expression avec `$(...)`.

## Concaténer et interpoler

```sx
func main() {
    let name = "Ada"
    let answer = 42
    let greeting = "Bonjour, " + name

    print(greeting)
    print("La réponse est $(answer)")
}
```

Le programme affiche :

```text
Bonjour, Ada
La réponse est 42
```

Seule la séquence `$(` commence une interpolation. L'expression placée entre
parenthèses est évaluée une seule fois. Écrivez `$$` pour obtenir un dollar
littéral : `"$$(answer)"` produit le texte `$(answer)`.

## Échapper un caractère

Les chaînes reconnaissent notamment `\\`, `\"`, `\n`, `\r`, `\t`, `\0` et
`\u{H...}`. La dernière forme écrit un scalaire Unicode à partir de sa valeur
hexadécimale :

```sx
let quoted = "\"Silex\""
let smile = "\u{1F642}"
```

## Écrire une chaîne sur plusieurs lignes

Un guillemet immédiatement suivi d'un retour à la ligne commence une chaîne en
bloc :

```sx
let paragraph = "
    Première ligne.
    Deuxième ligne.
    "
```

Sa valeur est `Première ligne.\nDeuxième ligne.`. Silex retire les retours à
la ligne structurels d'ouverture et de fermeture, puis retire de chaque ligne
non vide l'indentation placée avant le guillemet fermant. Une indentation
supplémentaire reste dans la valeur. Tous les retours à la ligne du fichier
source sont normalisés en `\n`.

Le guillemet fermant doit commencer sa ligne, après une éventuelle indentation.
Un guillemet contenu ailleurs sur une ligne s'écrit `\"`. Les chaînes en bloc
acceptent les mêmes échappements et interpolations que les chaînes ordinaires.

## Compter et comparer le texte

`count()` renvoie le nombre de scalaires Unicode, pas le nombre d'octets UTF-8.
Les opérateurs `==` et `!=` comparent en revanche les octets UTF-8 exacts ; ils
n'effectuent aucune normalisation Unicode.

[Revenir aux valeurs](README.md) ·
[Calculer et comparer](Operators.md)
