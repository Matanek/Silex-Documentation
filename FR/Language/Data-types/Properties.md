# Contrôler un accès avec une propriété

Une propriété conserve la déclaration familière d'un champ, puis place ses
accesseurs dans un bloc. `let` expose une lecture seule ; `var` peut aussi
exposer une écriture.

```sx
struct Rectangle {
    let width:int
    let height:int

    let area:int {
        get { return self.width * self.height }
    }
}

struct Thermometer {
    var raw:int

    var celsius:int {
        get { return self.raw }
        set(value) { self.raw = value }
    }
}
```

`Rectangle(width:6, height:7).area` appelle le getter. L'affectation exacte
`thermometer.celsius = 21` appelle le setter avec `21` dans son paramètre
explicite `value`. Un setter ne retourne aucune valeur et ne constitue pas un
site de propagation pour `try` ou `Result` : une écriture susceptible d'échouer
doit rester une méthode explicite.

`get` et `set` sont contextuels. Ils restent disponibles comme identifiants
ordinaires hors du bloc d'une propriété.

## Distinguer la mutabilité du calcul

Un getter ne peut pas modifier `self`. Cette règle rend toute lecture valide
indépendamment du fait que le récepteur ait été lié avec `let` ou `var` : le
lecteur n'a pas à connaître l'implémentation de la propriété pour savoir si
`value.property` est autorisé. Un compteur d'accès ou un cache attaché à
l'instance doit donc passer par une méthode explicite.

Un setter peut modifier `self`. Il appartient en conséquence à une propriété
`var` ; une propriété `let` ne peut déclarer que `get`.

Une affectation composée lit puis écrit la propriété exactement une fois de
chaque côté :

```sx
thermometer.celsius += 4
```

équivaut, pour les effets observables, à un appel du getter suivi d'un appel du
setter. La valeur du récepteur n'est pas réévaluée inutilement.

## Modifier une valeur obtenue par lecture

Seule l'affectation de la propriété elle-même appelle son setter :

```sx
player.position = Position(x:10) // appelle le setter de position
```

Une sélection supplémentaire travaille sur le résultat du getter. Si ce
résultat est une structure, il s'agit d'une valeur temporaire et sa mutation
est refusée :

```sx
player.position.x = 10 // refusé si Position est une structure
```

Il faut alors construire puis affecter une nouvelle position. Si le getter
retourne une classe, le résultat conserve au contraire son identité partagée :
`player.position.x = 10` modifie cette instance sans rappeler le setter de
`position`.

## Initialiser paresseusement un membre statique

Une propriété possède un stockage caché distinct de son type public. Dans ses
propres accesseurs seulement, son nom désigne ce stockage optionnel. Un getter
statique peut ainsi effectuer une première initialisation sans déclarer un
second champ :

```sx
class Data {
    static let instance:Data {
        get {
            if Data.instance == null {
                Data.instance = Data()
            }
            return Data.instance
        }
    }

    private init() {}
}
```

À l'extérieur du getter, `Data.instance` a le type `Data` et appelle le getter.
À l'intérieur, le même nom permet de tester puis d'initialiser le stockage. Le
retour l'extrait vers le type public ; quitter le getter avant son
initialisation provoque donc l'échec d'une extraction optionnelle forcée. Les
getters statiques sont sérialisés : deux accès concurrents ne peuvent pas
initialiser ce stockage simultanément.

## Initialisation, réflexion et protocoles

Une propriété calculée n'est pas un champ. Elle ne participe jamais à
l'initialiseur automatique par champs nommés. `reflect(value).fields` contient
le stockage visible, `reflect(value).properties` les propriétés visibles et
`reflect(value).methods` les méthodes visibles ; les accesseurs générés ne
sont pas exposés comme méthodes.

Un protocole exprime le contrat d'une propriété sans introduire de stockage :

```sx
protocol Named {
    name:str { get }
}

protocol Renamable {
    name:str { get set }
}
```

Un champ `let` ou `var` satisfait une exigence `{ get }` du même nom et du même
type. Seul un champ `var`, ou une propriété possédant les deux accesseurs,
satisfait `{ get set }`. Les mots `let` et `var` restent interdits dans un
protocole : celui-ci décrit un accès, jamais la représentation qui le fournit.

[Revenir aux types de données](README.md) ·
[Définir un contrat avec un protocole](Protocols.md)
