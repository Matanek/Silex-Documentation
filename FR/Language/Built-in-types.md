# Choisir un type intégré

Silex fournit des types intégrés pour les nombres, les valeurs logiques et le
texte. Le compilateur peut souvent les déduire depuis la valeur initiale ; une
annotation explicite reste utile lorsqu'une largeur précise est nécessaire.

## Laisser Silex déduire le type

Dans ce programme, les quatre premières déclarations sont déduites. Seul
`channel` impose son type :

```sx
func main() {
    let count = 42
    let ratio = 0.5
    let ready = true
    let title = "Silex"
    let channel:uint8 = 255

    print(count)
    print(ratio)
    print(ready)
    print(title)
    print(channel)
}
```

Le compilateur choisit respectivement `int`, `float`, `bool` et `str` pour
`count`, `ratio`, `ready` et `title`.

Le programme affiche :

```text
42
0.5
true
Silex
255
```

## Connaître les types disponibles

| Valeur | Types disponibles | Nom courant |
| --- | --- | --- |
| Entier signé | `int8`, `int16`, `int32`, `int64`, `int` | `int` |
| Entier non signé | `uint8`, `uint16`, `uint32`, `uint64`, `uint` | `uint` |
| Nombre à virgule flottante | `float32`, `float64`, `float` | `float` |
| Valeur logique | `bool` | `bool` |
| Texte | `str` | `str` |

Les noms courants correspondent à des largeurs précises : `int` est un
`int64`, `uint` est un `uint64` et `float` est un `float32`.

## Choisir une largeur explicite

Utilisez les noms courants pour les calculs ordinaires. Choisissez une largeur
explicite lorsqu'une valeur doit respecter une plage déterminée, par exemple
`uint8` pour une composante comprise entre `0` et `255`, ou `float64` lorsque
le calcul demande davantage de précision.

Une annotation de type suit directement le nom, sans espace autour de `:` :
`channel:uint8`.

Si une déclaration typée n'a pas de valeur après `=`, consultez
[les valeurs initiales choisies par Silex](Default-values.md).

[Revenir aux concepts du langage](README.md) ·
[Comprendre les variables](Variables.md)
