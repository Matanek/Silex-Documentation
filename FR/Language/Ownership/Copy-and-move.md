# Copier ou transférer une valeur

Une affectation ordinaire copie compositionnellement les structures, enums,
optionnels, tableaux et listes. Les références de classes qu'ils contiennent
restent partagées.

```sx
struct Position {
    var x:int
    var y:int
}

func main() {
    var first = Position(x:1, y:2)
    var second = first
    second.x = 10

    print(first.x)
    print(second.x)
}
```

Après la copie suivante, les deux structures atteignent encore la même
instance `State` :

```sx
var foo1 = Foo(value:10, instance:State(value:5))
var foo2 = foo1

foo2.instance.value = 8
print(foo1.instance.value) // 8
```

## Détacher tout le graphe atteignable

```sx
var foo3 = copy foo1

foo3.instance.value = 12
print(foo1.instance.value) // 8
```

`copy` recrée récursivement les instances de classes atteintes. Des références
répétées restent répétées dans le clone et les cycles restent des cycles. Aucun
constructeur n'est rappelé.

Une classe déclarée `nocopy` représente une identité native ou externe
qui ne possède pas de duplication sûre : device, buffer, fichier système ou
ressource comparable. L'affectation ordinaire continue d'en partager
l'identité, directement ou à travers une structure. En revanche, `copy` est
refusé statiquement dès que la valeur atteint une telle classe : il ne duplique
jamais silencieusement un handle et son nettoyage.

```sx
nocopy class DeviceHandle {}
struct Owner { var handle:DeviceHandle }

var handle = DeviceHandle()
let owner = Owner(handle:handle)
let shared = owner       // partage la même identité
// let detached = copy owner // erreur : classe non clonable atteinte
```

Le compilateur capture un instant logique cohérent du graphe. Une mutation
Silex concurrente est ordonnée avant ou après cet instant ; la copie détachée
ne peut pas mélanger deux états de la source.

## Transférer la possession

```sx
var original = Position(x:1, y:2)
let transferred = move original

original = Position()
```

`move` consomme une variable locale complète ou un paramètre ordinaire. La
source ne peut plus être lue avant qu'un `var` consommé reçoive une valeur
complète de remplacement. Un `let` consommé ne peut pas être réinitialisé.

La source consommée n'exécute plus `drop` ; la valeur transférée possède le
nettoyage restant. Les champs, éléments indexés, `self` et expressions
temporaires ne sont pas des sources acceptées pour un `move` explicite.

[Revenir à la possession](README.md) ·
[Emprunter sans transférer](References.md)
