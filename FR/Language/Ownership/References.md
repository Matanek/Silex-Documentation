# Emprunter une valeur

Les modes de référence appartiennent aux paramètres et aux retours. La syntaxe
d'appel reste ordinaire.

## Emprunter en lecture

```sx
struct Box {
    let value:int
    func get() int { return self.value }
}

func inspect(box:@Box) int {
    return box.get()
}

func main() {
    let box = Box(value:42)
    print(inspect(box))
}
```

`@T` exprime une lecture du chemin emprunté. La fonction peut le lire ou le
transmettre à un autre `@T`, mais ne peut ni le modifier, ni le transférer, ni
le stocker, ni retourner directement cette capacité.

Elle peut retourner une copie ordinaire lorsque `T` est une valeur qui ne peut
pas laisser échapper une référence de classe atteinte :

```sx
func copied(value:@int) int { return value }
```

Pour une classe, les champs et méthodes atteints par le chemin emprunté restent
en lecture seule. Un alias indépendant peut toujours modifier la même instance :
`@` ne gèle pas globalement l'objet.

## Emprunter pour modifier

```sx
func increment(value:&int) {
    value += 1
}

var count = 1
increment(count)
print(count) // 2
```

`&T` désigne l'emplacement modifiable de l'appelant. Les écritures sont visibles
dans le `var`, le champ ou l'élément indexé fourni.

Pour une classe, un paramètre ordinaire `Class` peut modifier l'instance partagée
mais ne peut pas remplacer la référence de l'appelant. `&Class` peut faire les
deux.

Comme `@T`, `&T` peut produire une copie ordinaire si aucune capacité empruntée
ne peut s'échapper. Retourner explicitement `@T` ou `&T` reste la manière de
renvoyer un alias plutôt qu'une copie.

## Retourner un emprunt

L'expression de retour ne répète pas `@` ou `&` :

```sx
func inspect(owner:@Owner) @State {
    return owner.state
}

func edit(owner:&Owner) &State {
    return owner.state
}
```

Avec un seul paramètre compatible, la provenance est implicite. Si plusieurs
paramètres peuvent être la source, nommez-la dans le type de retour :

```sx
func choose(first:@State, second:@State) @first:State {
    return first
}
```

Un résultat emprunté peut vivre dans une variable locale lexicale, mais ne peut
ni dépasser la durée de vie de sa racine ni être stocké dans un agrégat.

`T`, `@T` et `&T` ne forment pas des signatures de surcharge distinctes.

[Revenir à la possession](README.md) ·
[Emprunter une vue de collection](../Collections/Views.md)
