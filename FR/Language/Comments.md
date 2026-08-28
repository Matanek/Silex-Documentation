# Écrire un commentaire

Silex accepte deux formes de commentaire de ligne et une forme de commentaire
de bloc. Utilisez `//` dans les exemples et le code officiel pour conserver un
style cohérent.

## Commenter jusqu'à la fin de la ligne

`//` et `#` ignorent tout ce qui les suit jusqu'au retour à la ligne. Les deux
formes peuvent commencer une ligne ou suivre une instruction.

```sx
// Explique pourquoi cette valeur est nécessaire.
let answer = 42 # Cette forme est également acceptée.
```

## Commenter une région

`/*` ouvre un commentaire de bloc et `*/` le referme. Le bloc peut occuper une
partie de ligne ou plusieurs lignes. Il peut aussi contenir d'autres blocs :

```sx
func main() {
    /* Désactive temporairement cette région.
        /* Un bloc imbriqué reste correctement délimité. */
    */
    let answer = 40 /* Cette partie est ignorée. */ + 2
    print(answer)
}
```

Un bloc non refermé produit un diagnostic à l'emplacement de son `/*`. Un
retour à la ligne placé dans un bloc conserve son rôle de fin d'instruction :
gardez donc un commentaire inséré au milieu d'une expression sur la même ligne.

Les marqueurs placés dans une chaîne restent du texte ordinaire :

```sx
let text = "# // /* ceci n'est pas un commentaire */"
```

[Revenir aux concepts du langage](README.md) ·
[Retrouver une forme syntaxique](../Reference/Syntax.md)
