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

## Initialiser un champ à sa déclaration

La valeur écrite après `=` peut être une expression ordinaire : appel de
fonction, construction, calcul ou cascade. Elle est évaluée dans l'ordre des
champs pour chaque nouvelle instance, avant le corps de son constructeur.
Deux instances ne partagent donc pas accidentellement la collection créée par
une même expression.

```sx
func initial_values() int[] { return [1] }

class Recorder {
    var values:int[] = initial_values()
}

func main() {
    var first = Recorder()
    var second = Recorder()
    first.values.append(2)

    print(first.values.count())  // 2
    print(second.values.count()) // 1
}
```

Cette forme convient lorsque la valeur ne dépend ni de `self` ni d'un argument
du constructeur : ces noms ne sont pas disponibles dans l'expression. Elle
peut notamment préparer un objet par cascade sans imposer un optionnel
temporaire.

## Construire une instance

Sans constructeur personnalisé, initialisez les champs visibles par leur nom :

```sx
class Position {
    var x:int
    var y:int
}

func main() {
    var position = Position(x:2, y:3)
    var target = Position()
        ..x = position.x
        ..y = position.y

    position.x = 4

    print(position.x)
    print(target.x)
    print(target.y)
}
```

La [cascade](../Values/Operators.md#appliquer-plusieurs-opérations-au-même-objet)
configure ici une nouvelle instance à partir de `position`. Le programme
affiche `4`, `2`, puis `3` : modifier ensuite `position` ne modifie pas
`target`, car chaque appel à `Position()` crée une identité distincte.

Déclarez `init` lorsque la construction doit établir un invariant. Dès qu'un
constructeur existe, l'initialiseur automatique par champs nommés disparaît.
Un champ `let` sans valeur déclarée et un champ `var` dont le type ne fournit
ni valeur intrinsèque ni construction sans argument restent alors en attente.
Le constructeur doit les affecter sur chaque chemin normal. Un champ déjà
initialisé peut être lu ; `self` dans son ensemble ne peut être utilisé qu'une
fois tous les champs initialisés.

```sx
struct Range {
    let minimum:float
    let maximum:float

    init(minimum:float, maximum:float) {
        self.minimum = minimum
        self.maximum = maximum
    }
}

class Gauge {
    var limits:Range

    init(minimum:float, maximum:float) {
        self.limits = Range(minimum, maximum)
    }
}
```

Dans `init`, une affectation `self.champ = valeur` initialise le champ visé ;
elle ne constitue pas un usage prématuré de l’objet complet. Un champ déjà
initialisé peut ensuite être lu ou modifié pendant que d’autres restent en
attente. Les boucles peuvent donc préparer un stockage dont la taille dépend
d’un argument :

```sx
class PreparedList {
    var values:int[]
    let requested_count:int

    init(count:int) {
        self.values = []
        var index = 0
        while index < count {
            self.values.append(index)
            index++
        }
        self.requested_count = count
    }
}
```

Lire un champ encore en attente ou transmettre `self` comme un objet complet
reste interdit. Une affectation présente uniquement dans une boucle qui peut
ne jamais s’exécuter ne suffit pas non plus à établir l’initialisation sur tous
les chemins ; le champ doit recevoir une valeur sur le chemin de sortie de la
boucle.

Les trois formes se complètent : placez une expression sur le champ pour une
valeur propre à chaque instance, utilisez `init` lorsque la valeur dépend de
ses arguments, et conservez l'initialiseur automatique par champs nommés
lorsque l'appelant doit fournir librement la configuration. Déclarez `T?`
uniquement lorsque l'absence de valeur appartient réellement au modèle ; ce
n'est pas un état intermédiaire requis par la construction.

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
