# Créer une valeur nominale avec une structure

Une structure définit un type valeur avec des champs nommés. Une affectation
ordinaire copie sa valeur.

```sx
struct Position {
    var x:int
    var y:int = 10
}

func main() {
    let origin = Position()
    var cursor = Position(x:2, y:3)
    var target = Position()
        ..x = 10
        ..y = 5

    cursor.x = 4

    print(origin.y)
    print(cursor.x)
    print(target.x)
    print(target.y)
}
```

Sans constructeur personnalisé, les champs sont nommés et peuvent apparaître
dans n'importe quel ordre. Un champ omis reçoit d'abord sa valeur déclarée,
puis à défaut la [valeur intrinsèque de son type](../Values/Default-values.md).
Ici, la [cascade](../Values/Operators.md#appliquer-plusieurs-opérations-au-même-objet)
configure `target` juste après sa création. Le programme affiche successivement
`10`, `4`, `10` et `5`.

La valeur déclarée après `=` accepte une expression ordinaire, y compris un
appel de fonction ou une cascade. Silex l'évalue dans l'ordre des champs pour
chaque nouvelle valeur : une liste ou une autre ressource ainsi créée
n'est pas partagée accidentellement entre deux constructions. Cette expression
n'a accès ni à `self` ni aux paramètres d'un constructeur.

## Copier une structure

```sx
var first = Position(x:1, y:2)
var second = first

second.x = 10
print(first.x) // 1
```

Les structures sont nominales : deux déclarations distinctes créent deux types
distincts, même avec les mêmes champs. Leur affectation copie récursivement les
champs. Un champ de classe continue toutefois de désigner la même instance
partagée. Utilisez [`copy`](../Ownership/Copy-and-move.md) lorsque tout le
graphe atteignable doit être détaché.

## Établir un invariant avec `init`

```sx
struct Position {
    let x:int
    let y:int

    init(value:int) {
        self.x = value
        self.y = value
    }
}

let point = Position(5)
```

Déclarer un seul `init` ferme l'initialiseur automatique par champs nommés.
Chaque champ `let` sans valeur déclarée doit alors être initialisé exactement
une fois sur chaque chemin normal du constructeur. Il en va de même pour un
champ `var` dont le type ne fournit ni valeur intrinsèque ni construction sans
argument. Les champs déjà initialisés peuvent être lus pour calculer les
suivants, mais `self` ne peut être utilisé dans son ensemble qu'après leur
initialisation complète.

Une expression de champ convient aux valeurs indépendantes des arguments,
`init` aux invariants qui en dépendent, et l'initialiseur automatique par
champs nommés aux valeurs que l'appelant doit choisir. Un type optionnel reste
réservé à une absence valide dans le modèle, pas au stockage provisoire d'un
champ en cours de construction.

## Ajouter des méthodes

```sx
struct Counter {
    var value:int

    func increment(amount:int = 1) {
        self.value += amount
    }

    func current() int {
        return self.value
    }
}
```

Une méthode reçoit `self` implicitement. Silex déduit si elle le modifie ; un
appel modifiable exige un récepteur déclaré avec `var`.

## Imbriquer un type

```sx
public struct Catalog {
    struct Entry {
        let value:int
    }
}

let entry = Catalog.Entry(value:42)
```

Un type imbriqué ne capture aucune instance du conteneur. Un alias ou une
réexportation publique du type extérieur conserve l'accès à ses types
imbriqués : un alias `Window` expose donc aussi `Window.Settings`.

Les [membres statiques](Static-members.md), les
[structures génériques](Generics.md) et leur [nettoyage](Cleanup.md) possèdent
leurs pages dédiées afin de ne pas mélanger ces contrats avec la sémantique de
valeur ordinaire.

[Revenir aux types de données](README.md) ·
[Partager une identité avec une classe](Classes.md)
