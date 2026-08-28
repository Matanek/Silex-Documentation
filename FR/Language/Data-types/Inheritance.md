# Spécialiser une classe par héritage

Une classe peut étendre au plus une classe de base. La base est construite
avant la classe dérivée.

```sx
class Entity {
    let position:int

    init(position:int) {
        self.position = position
    }
}

class Player:Entity {
    let name:str

    init(name:str, position:int):super(position) {
        self.name = name
    }
}

func main() {
    var entity:Entity = Player("Ada", 42)
    print(entity.position)
}
```

Omettre `:super(...)` équivaut à appeler `:super()`.

## Redéfinir une méthode

```sx
class Entity {
    func update() {
        print("entité")
    }
}

class Player:Entity {
    override func update() {
        super.update()
        print("joueur")
    }
}
```

Toute méthode d'instance héritée et non privée peut être redéfinie. La
résolution de surcharge utilise le type visible du récepteur ; la méthode ainsi
sélectionnée est ensuite distribuée selon la classe réelle de l'instance.

Les constructeurs, méthodes privées, méthodes statiques et méthodes ajoutées
par une extension ne sont pas virtuels.

## Conserver le contrat de mutation

La capacité à modifier le récepteur appartient au contrat de la méthode
héritée. Une redéfinition ne peut pas commencer à modifier `self` si la méthode
de base est en lecture seule. Inversement, redéfinir une méthode modifiable
conserve ce contrat même si le nouveau corps n'écrit pas lui-même dans `self` ;
les appels dynamiques continuent de préserver l'état du récepteur attendu par
l'emplacement de base.

Une classe dérivée hérite également des conformances valides aux protocoles de
sa base. Le [nettoyage](Cleanup.md) suit la classe dynamique vers ses bases.

[Revenir aux types de données](README.md) ·
[Définir un protocole](Protocols.md)
