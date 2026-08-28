# Convertir un nombre

L'opérateur `as` convertit une valeur numérique vers le type demandé. La
conversion est contrôlée : Silex l'interrompt si le type cible ne peut pas
représenter la valeur sans perte.

## Demander une conversion explicite

Ce programme convertit le même entier en `uint8` et en `float64` :

```sx
func main() {
    let count = 42
    let byte = count as uint8
    let precise = count as float64

    print(byte)
    print(precise)
}
```

Il affiche :

```text
42
42.0
```

Le type cible vient après `as`. Le compilateur déduit donc `uint8` pour
`byte` et `float64` pour `precise`.

## Comprendre le contrôle effectué

Une conversion explicite doit conserver la valeur exacte. Par exemple,
`255 as uint8` réussit, tandis que `256 as uint8` échoue parce qu'un `uint8`
ne peut pas contenir `256`. De même, un nombre à virgule ne peut devenir un
entier que s'il se trouve dans sa plage et ne possède aucune partie
fractionnaire.

Certaines conversions qui élargissent le type sont implicites, notamment
`int8` vers `int64`, `uint8` vers `uint64` et `float32` vers `float64`. Les
autres changements de famille ou de largeur peuvent exiger `as`. Vous pouvez
aussi l'écrire lorsqu'une conversion implicite gagnerait à rester visible.

`as` ne convertit que des valeurs numériques. Il ne sert pas à transformer un
nombre en texte ni à changer le type d'une autre valeur.

[Revenir aux concepts du langage](README.md) ·
[Choisir un type intégré](Built-in-types.md)
