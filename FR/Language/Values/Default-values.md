# Omettre une valeur initiale

Une déclaration qui indique son type peut parfois omettre la valeur placée
après `=`. Silex initialise alors la variable avec la valeur intrinsèque de ce
type. La variable reçoit donc toujours une valeur déterminée.

## Utiliser la valeur intrinsèque

Ce programme laisse Silex initialiser trois valeurs usuelles :

```sx
func main() {
    let count:int
    let ratio:float
    let ready:bool

    print(count)
    print(ratio)
    print(ready)
}
```

Le programme affiche :

```text
0
0.0
false
```

L'annotation est indispensable lorsque la valeur initiale est absente : sans
elle, le compilateur ne peut pas déterminer le type à créer.

## Retrouver les valeurs usuelles

| Type | Valeur intrinsèque |
| --- | --- |
| Entier signé ou non signé | `0` |
| `float32`, `float64` ou `float` | `0.0` |
| `bool` | `false` |
| `str` | chaîne vide `""` |
| Type optionnel `T?` | `null` |

## Initialiser une classe

Une classe ne possède pas d'instance intrinsèque. Une variable de classe doit
donc recevoir une instance, comme dans `var player = Player()`. Si l'absence
d'instance est valide, déclarez plutôt un optionnel avec
`var selected:Player?` ; sa valeur initiale sera `null`.

Dans un constructeur, cette règle ne force pas un champ sans valeur
intrinsèque à devenir optionnel. Le champ peut rester en attente puis recevoir
sa première valeur par `self.field = value`, à condition que chaque chemin
normal l'initialise avant toute lecture ou utilisation de `self`.

[Revenir aux valeurs](README.md) ·
[Choisir un type intégré](Built-in-types.md)
