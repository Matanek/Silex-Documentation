# Emprunter une vue de collection

Une vue partage une tranche de stockage sans posséder ses éléments. `@` crée
une vue en lecture et `&` une vue modifiable.

```sx
func main() {
    var values = [10, 20, 30, 40, 50]
    {
        let middle = @values[1:4]
        print(middle[0])
    }

    {
        var editable = &values[1:4]
        editable[0] = 42
    }
    print(values[1])
}
```

Les deux bornes sont obligatoires. Le début est inclus, la fin exclue. Les
bornes négatives sont rendues relatives à `count()`, puis les deux bornes sont
limitées aux limites de la collection.

## Accepter une vue

```sx
func sum(values:@int[..]) int {
    var total = 0
    for value in values {
        total += value
    }
    return total
}
```

## Retourner une vue

```sx
func identity(values:@int[..]) @values:int[..] {
    return values
}
```

Une vue peut vivre dans une liaison lexicale ou traverser un paramètre et un
retour empruntés dont la provenance est déclarée. Elle ne possède ni stockage
ni éléments.

Les vues partagées et modifiables acceptent `count`, `is_empty`, l'indexation,
les sous-vues et l'itération. Une vue modifiable accepte aussi les écritures
indexées, `for var` et `swap`. Aucune vue ne peut redimensionner, réordonner
globalement, retirer ou transférer des éléments.

Une vue ne peut pas être stockée dans un optionnel, une structure, un enum, une
collection, un champ statique ou une capture. Une tranche sans `@` ni `&` reste
une liste copiée et indépendante.

[Revenir aux collections](README.md) ·
[Comprendre les références](../Ownership/References.md)
