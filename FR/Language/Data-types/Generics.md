# Adapter un type à plusieurs types concrets

Une structure, une classe ou un enum générique décrit une famille de types. Une
utilisation fournit toujours la liste complète des arguments de type.

## Spécialiser une structure

```sx
struct Pair<T> {
    let first:T
    let second:T
}

func main() {
    let pair = Pair<int>(first:1, second:2)
    print(pair.first)
}
```

`Pair<int>` et `Pair<str>` sont deux types concrets distincts. Une méthode
d'une structure non générique peut déclarer ses propres paramètres de type.

## Spécialiser une classe

```sx
class Box<T> {
    let value:T

    init(value:T) {
        self.value = value
    }
}

func main() {
    var box = Box<int>(42)
    print(box.value)
}
```

Chaque spécialisation conserve la sémantique d'identité ordinaire d'une classe
et possède son propre stockage statique. Ses méthodes peuvent employer les
paramètres de la classe, mais ne peuvent pas ajouter une seconde liste de
paramètres de type.

## Spécialiser un enum

```sx
enum Outcome<T,E> {
    success(T)
    failure(E)
}

func main() {
    let outcome = Outcome<int,str>.success(42)
    let value = match outcome {
        success(number) => number
        failure(error) => 0
    }
    print(value)
}
```

La construction d'une variante et `match` n'infèrent pas les arguments du type
enum : la liste complète reste explicite.

Les contraintes de protocole et l'inférence des fonctions sont décrites dans
[les fonctions génériques](../Functions/Generics.md).

[Revenir aux types de données](README.md) ·
[Définir un protocole](Protocols.md)
