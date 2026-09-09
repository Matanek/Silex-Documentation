# Observer les métadonnées d'une valeur

La fonction fournie par le compilateur `reflect` retourne des métadonnées au
niveau source sans modifier ni consommer sa valeur.

```sx
func main() {
    let value = 42
    let metadata = reflect(value)
    print(metadata.type)
}
```

`type` contient l'écriture canonique du type de la valeur. Pour une classe, il
s'agit du type concret à l'exécution, même lorsque l'expression possède un type
de base. Pour les autres valeurs, il correspond au type connu statiquement.
`name` contient la déclaration source canonique représentée par l'expression :
type concret d'une classe, type nominal, variante d'enum, membre sélectionné ou
fonction nommée. Un scalaire calculé et sans nom expose seulement `type` ;
demander son `name` produit un diagnostic à la compilation.

Pour un enum, `name` suit sa variante active. Les noms de packages et de
modules nommés contiennent leur chemin canonique complet, tel que
`GFX.Animation.Easing.constant`. Le nom interne du module d'entrée, souvent
`Main`, est omis. Un alias local ne change jamais cette identité.

## Observer un membre sélectionné

```sx
struct Foo { let name:str = "nom de Foo" }

assert(reflect(Foo().name).name == "Foo.name")
assert(reflect(Foo().name).type == "str")
```

Le nom conserve le chemin de la déclaration ; `type` décrit la valeur
sélectionnée.

## Inspecter les variantes d'un enum

`variants` fournit chaque nom de variante dans l'ordre source. Une variante
avec contenu ne contribue que son nom :

```sx
enum Message {
    empty
    text(str)
}

let metadata = reflect(Message.empty)
assert(metadata.variants[0] == "empty")
assert(metadata.variants[1] == "text")
```

## Inspecter une structure ou une classe

Une classe expose aussi `types`, sa lignée d'héritage complète, de la racine au
type concret. Le dernier élément est donc toujours égal à `type` :

```sx
class Node {}
class Player : Node {}
class Captain : Player {}

var node:Node = Captain()
let metadata = reflect(node)

assert(metadata.type == "Captain")
assert(metadata.name == "Captain")
assert(metadata.types[0] == "Node")
assert(metadata.types[1] == "Player")
assert(metadata.types[2] == metadata.type)
```

Une structure n'expose pas `types`, car elle n'a pas d'identité polymorphe à
l'exécution.

`fields`, `properties` et `methods` contiennent respectivement les champs, les
propriétés et les méthodes d'instance visibles au site d'appel, dans leur ordre
de déclaration. Les accesseurs générés d'une propriété ne sont jamais répétés
dans `methods`. Les portées `private`,
`protected`, `package`, `module` et `local` continuent de s'appliquer : la
réflexion ne révèle rien que le code ordinaire ne puisse déjà atteindre au
même endroit. Les membres statiques ne font pas partie de la réflexion d'une
instance.

## Inspecter une fonction

```sx
func predicate(value:int) bool { return value > 0 }
let metadata = reflect(predicate)

assert(metadata.type == "func(int)bool")
assert(metadata.name == "predicate")
assert(metadata.parameters[0] == "int")
assert(metadata.return_type == "bool")
```

Les modes empruntés restent présents dans l'écriture du type. `reflect` évalue
son argument exactement une fois et ne le transfère pas. Le compilateur émet
seulement les chaînes et listes ordinaires demandées par la catégorie ; aucune
adresse mémoire, position de champ, symbole machine ou ABI stable n'est exposé.
Les noms retournés décrivent le programme source ; ils ne constituent pas des
identifiants de sérialisation stables.

[Revenir aux types de données](README.md) ·
[Comprendre les classes intrinsèques](Intrinsic-classes.md)
