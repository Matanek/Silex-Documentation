# Choisir un tableau ou une liste

Un tableau fixe encode sa taille dans son type. Une liste dynamique peut être
redimensionnée.

## Employer un tableau fixe

```sx
func main() {
    var axes:int[3] = [10, 20, 30]
    let empty:int[0] = []

    axes[-1] = 40
    print(axes.count())
    print(empty.is_empty())
}
```

Un indice négatif compte depuis la fin. Un indice hors limites termine le
programme avec un diagnostic de bornes.

## Employer une liste dynamique

```sx
var scores:int[] = []
let inferred = [10, 20, 30]

scores = inferred
scores[-1] = 40
```

Un littéral vide demande un type attendu. Un littéral non vide déduit son type
d'élément depuis la première valeur.

## Modifier une collection

Tableaux et listes acceptent `swap`, `reverse` et `replace`. Les listes
acceptent aussi `append`, `prepend`, `insert`, `take`, `take_first`,
`take_last` et `clear`. Chaque opération modifiable exige un récepteur `var`.

```sx
values.swap(0, 2)
values.reverse()
let previous = values.replace(1, 42)

items.append(item)
items.prepend(item)
items.insert(1, item)
let removed = items.take(0)
items.clear()
```

Les champs d'une structure stockée peuvent être modifiés directement à travers
un indice. Silex reconstruit le chemin valeur dans la collection tout en
préservant sa sémantique de valeur :

```sx
vertices[index].position = vertices[index].position.add(offset)
vertices[index].color.a = 0.5
```

Chaque champ traversé doit être modifiable et la collection doit être atteinte
par une liaison ou une référence modifiable.

## Copier une tranche

```sx
let middle = values[1:4]
```

Les deux bornes sont obligatoires. Le début est inclus, la fin exclue et les
bornes négatives sont relatives à `count()`. Le résultat est une liste
indépendante. Utilisez une [vue](Views.md) lorsque la tranche doit emprunter le
stockage d'origine.

Lorsque les éléments déclarent `drop`, chaque copie de collection possède ses
propres valeurs. `clear()` les détruit du dernier indice vers le premier.

[Revenir aux collections](README.md) ·
[Parcourir des valeurs](Iteration.md)
