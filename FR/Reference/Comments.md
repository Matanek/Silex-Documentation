# Écrire un commentaire

Silex 0.42 reconnaît `//` comme commentaire de ligne. Tout le texte situé après
ce marqueur est ignoré jusqu'au retour à la ligne.

```sx
// Explique pourquoi cette valeur est nécessaire.
let answer = 42 // Un commentaire peut suivre une instruction.
```

Le marqueur placé dans une chaîne reste du texte ordinaire :

```sx
let address = "https://silex-lang.org/"
```

Utilisez `//` dans les exemples et le code officiel. Les commentaires `#` et
les blocs `/* ... */` ne font pas partie du langage Silex 0.42.

[Revenir à la référence](README.md) ·
[Retrouver une forme syntaxique](Syntax.md)
