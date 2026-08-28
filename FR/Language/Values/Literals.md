# Écrire une valeur littérale

Un littéral inscrit directement une valeur dans le code source. Silex 0.42
reconnaît les formes suivantes.

## Nombres

```sx
let decimal = 1_000
let binary = 0b1010
let octal = 0o12
let hexadecimal = 0xFF
let exponent = 1.5e2
```

Un soulignement peut séparer les chiffres d'un nombre. Les préfixes `0b`,
`0o` et `0x` sélectionnent respectivement les bases 2, 8 et 16. Un nombre avec
un point ou un exposant est un nombre à virgule flottante.

## Booléens, texte et absence

```sx
let enabled = true
let disabled = false
let line = "première ligne\ndeuxième ligne"
let scalar = "\u{1F642}"
let missing:Item? = null
```

Les chaînes ordinaires, leurs échappements, les chaînes en bloc et
l'interpolation sont détaillés dans
[la documentation du texte](Strings.md). `null` possède un
type optionnel déterminé par son contexte.

[Revenir aux valeurs](README.md) ·
[Choisir un type intégré](Built-in-types.md)
