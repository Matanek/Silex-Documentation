# Transmettre une fonction de rappel

Une fonction peut devenir une valeur, être rangée dans un champ ou être passée
à une autre fonction. Son type décrit les modes de ses paramètres et son type
de retour.

## Nommer une fonction existante

```sx
func any<T>(values:T[], predicate:func(@T) bool) bool {
    for value in values {
        if predicate(value) {
            return true
        }
    }
    return false
}

func positive(value:@int) bool { return value > 0 }

func main() {
    print(any<int>([-1, 2], positive))
}
```

Écrivez `func(T)` pour un callback qui renvoie `void`, et `func(T) R` pour un
callback qui renvoie `R`. Les paramètres conservent les modes ordinaires de
Silex : valeur, référence en lecture `@` ou référence modifiable `&`. Le type
attendu permet de choisir une fonction surchargée.

## Lier une méthode à son instance

Une méthode d'instance peut être extraite de son récepteur. La valeur obtenue
garde ce récepteur lié : `self` n'apparaît donc pas dans le type
du callback.

```sx
var parser = Parser(source)
let read_next:func() Token = parser.next_token
let first = read_next()
```

Le récepteur est évalué une seule fois lors de l'extraction. Une méthode
de structure modifiable exige un récepteur stable et modifiable, puis écrit
au même endroit à chaque appel. Le type attendu sélectionne une surcharge et
peut omettre les derniers paramètres possédant une valeur par défaut.

Pour une structure, la méthode liée garde son récepteur emprunté. Une méthode
en lecture en empêche la modification ; une méthode modifiable le réserve
exclusivement.
Utilisez une portée anonyme lorsque le récepteur doit être réutilisé ensuite :

```sx
var counter = Counter(value:0)
{
    let increment:func() = counter.increment
    increment()
}
print(counter.value)
```

Une méthode liée à une classe possédée conserve l'identité de son objet. Elle
peut être retournée ou stockée durablement, même après la fin de la portée qui
l'a créée. Réaffecter la variable d'origine ne change pas son récepteur.

```sx
class Counter {
    var value:int = 0
    func add(amount:int) int { self.value += amount; return self.value }
}

func make_counter() func(int) int {
    var counter = Counter()
    return counter.add
}

func main() {
    let add = make_counter()
    print(add(2)) // 2
    print(add(3)) // 5
}
```

Les copies ordinaires du callback partagent le même récepteur ; `copy` détache
son graphe d'objets. La dernière référence libère le récepteur, et le collecteur
traite aussi les cycles passant par des callbacks. Les surcharges et la
répartition virtuelle conservent les règles des appels de méthodes ordinaires.
Une méthode extraite à travers `@Class` ou `&Class` conserve toutefois l'emprunt
lexical de cette référence.

Si le récepteur atteint une classe `nocopy`, l'opération `copy` échoue à l'exécution.

## Écrire une fonction anonyme

```sx
let found = any<int>([-1, 2], func(value:@int) bool {
    return value > 0
})
```

Le type de retour s'omet lorsque la fonction anonyme renvoie `void` :

```sx
func visit(value:int, callback:func(int)) { callback(value) }
visit(42, func(value:int) { print(value) })
```

## Capturer une liaison extérieure

Une fonction anonyme capture seulement les liaisons extérieures qu'elle
utilise. Un `var` capturé reste partagé : le modifier dans la fonction modifie
la variable environnante, et les copies de la valeur de fonction désignent la
même liaison. Un `let` capturé reste immuable.

```sx
var count = 0
var increment = func() { count += 1 }
var same_increment = increment

increment()
same_increment()
print(count) // 2
```

Une fonction anonyme imbriquée peut capturer une liaison de n'importe quel
niveau lexical parent ; les niveaux intermédiaires transportent ce contexte
automatiquement.

Les captures anonymes et les méthodes liées à des structures sont des emprunts
lexicaux. Elles ne copient pas la valeur capturée et ne prolongent pas sa durée
de vie. Une fonction avec captures, ou une méthode liée à une structure locale,
peut être passée à un
appel synchrone mais ne peut pas être retournée hors de la portée qui possède
ses emprunts.

Les valeurs de fonction sont des valeurs du langage. Elles n'exposent ni
adresse machine ni convention d'appel de plateforme.

[Revenir aux fonctions](README.md) ·
[Comprendre les emprunts](../Ownership/References.md)
