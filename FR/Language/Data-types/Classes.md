# Partager une identité avec une classe

Une classe définit des objets partagés à durée de vie automatique. Passer,
copier ou ranger une valeur de classe conserve l'identité de la même instance.

```sx
public class Player {
    private let name:str
    var health:int = 100

    init(name:str) {
        self.name = name
    }

    func damage(amount:int) {
        self.health -= amount
    }

    func description() str {
        return "$(self.name) : $(self.health)"
    }
}

func main() {
    var player = Player("Ada")
    player.damage(10)
    print(player.description())
}
```

Utilisez `var` pour une liaison qui permet d'atteindre l'état modifiable d'une
classe, même lorsque cette liaison ne change jamais d'instance.

## Construire une instance

Sans constructeur personnalisé, initialisez les champs visibles par leur nom :

```sx
class Position {
    var x:int
    var y:int
}

var position = Position(x:2, y:3)
```

Déclarez `init` lorsque la construction doit établir un invariant. Dès qu'un
constructeur existe, l'initialiseur automatique par champs nommés disparaît.
Chaque champ immuable doit être initialisé sur chaque chemin normal avant que
`self` puisse s'échapper.

## Ajouter des méthodes

Les méthodes reçoivent `self` implicitement et acceptent les arguments
positionnels, nommés et les valeurs par défaut des fonctions ordinaires. Silex
déduit si une méthode modifie l'instance ; un tel appel exige un accès `var` à
l'identité partagée.

Les champs, constructeurs et méthodes héritent de la visibilité de la classe.
Un modificateur peut la restreindre sans jamais dépasser la visibilité du type.
Les portées exactes sont regroupées dans
[la visibilité des déclarations](../Modules/Visibility.md).

## Comparer des identités

`==` compare l'identité partagée de deux valeurs de classe, pas leurs champs.
Les règles de duplication et d'emprunt sont détaillées dans
[copier ou transférer une valeur](../Ownership/Copy-and-move.md) et
[emprunter une valeur](../Ownership/References.md).

Le code source ne manipule jamais l'adresse d'un objet, son compteur de
références, son allocation ou une libération manuelle. L'héritage, les membres
statiques, les spécialisations génériques et le nettoyage possèdent chacun un
contrat dédié.

[Revenir aux types de données](README.md) ·
[Spécialiser une classe par héritage](Inheritance.md)
