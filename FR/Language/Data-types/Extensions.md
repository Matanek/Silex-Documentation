# Ajouter des méthodes avec une extension

Une extension ajoute des méthodes à une structure ou une classe existante sans
modifier ses champs ni son identité.

```sx
struct Counter {
    var value:int
}

extend Counter {
    func increment(amount:int = 1) {
        self.value += amount
    }

    static func zero() Counter {
        return Counter(value:0)
    }
}

func main() {
    var counter = Counter.zero()
    counter.increment(2)
    print(counter.value)
}
```

Le programme affiche `2`. Les champs viennent du type d'origine ; l'extension
apporte seulement `increment` et `zero`.

Une extension ne peut ajouter ni champ, ni constructeur, ni `drop`, ni
redéfinition, ni membre `protected`.

Importer le module d'une extension active ses méthodes publiques. Placer le
fichier à côté du type cible ne suffit pas. Une référence qualifiée peut aussi
charger un module enfant à la demande : après `use STD.Math`, employer
`Math.Vec3` charge cet enfant et active les extensions qu'il déclare. Les
enfants inutilisés ne sont jamais chargés avidement parce que leur espace de
noms parent est disponible.

## Ajouter une conformance

```sx
extend Sprite:Drawable {
    func draw() {
        print("sprite")
    }
}
```

La conformance est visible dans les fichiers qui activent le fournisseur de
l'extension avec `use`, directement ou transitivement.

Une extension cible une structure ou une classe existante non générique. Une
méthode d'extension sur ce type peut en revanche déclarer ses propres
paramètres de type.

[Revenir aux types de données](README.md) ·
[Organiser les imports](../Modules/Imports.md)
