# Définir un contrat avec un protocole

Un protocole déclare les méthodes qu'un type conforme doit fournir.

```sx
protocol Drawable {
    func draw()
}

struct Sprite:Drawable {
    func draw() {
        print("sprite")
    }
}

func main() {
    var drawable:Drawable = Sprite()
    drawable.draw()
}
```

La conformance est nominale : le type doit citer le protocole. Une méthode de
la bonne forme ne suffit pas à elle seule.

## Exiger une propriété

Un protocole peut demander une lecture, ou une lecture et une écriture, sans
imposer de stockage :

```sx
protocol Named {
    name:str { get }
}

protocol Renamable {
    name:str { get set }
}
```

Un champ `let` ou `var` compatible satisfait `{ get }`. Seul un champ `var`,
ou une propriété calculée avec getter et setter, satisfait `{ get set }`.
`let` et `var` ne sont pas admis dans le protocole lui-même. Consultez le
[contrat complet des propriétés](Properties.md).

## Conserver une valeur conforme

```sx
var drawable:Drawable = Sprite()
drawable.draw()
```

Une valeur de protocole expose seulement les exigences du protocole. Une
structure est copiée dans cette valeur dynamique ; une classe conserve son
identité partagée. Les appels à travers une valeur de protocole sont traités
comme modifiables, donc un récepteur direct se déclare avec `var`.

## Contraindre une spécialisation

```sx
func render<T:Drawable>(value:T) {
    value.draw()
}

render(Sprite())
```

Le compilateur spécialise la fonction pour le type concret. À ce site d'appel,
le protocole ne crée donc aucun conteneur effacé. Un paramètre de type accepte
au plus une contrainte de protocole.

Une classe peut citer d'abord une unique classe de base, puis des protocoles.
Une structure cite seulement des protocoles. Une classe dérivée hérite des
conformances valides de sa base.

[Revenir aux types de données](README.md) ·
[Ajouter une conformance avec une extension](Extensions.md)
