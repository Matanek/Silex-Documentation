# Adapter une fonction à plusieurs types

Un paramètre de type permet d'écrire un comportement une fois, puis de le
spécialiser avec le type réellement utilisé.

```sx
func identity<T>(value:T) T {
    return value
}

func main() {
    let inferred = identity(42)
    let explicit = identity<str>("Silex")
    print(inferred)
    print(explicit)
}
```

Fournissez tous les arguments de type ou laissez Silex les déduire tous depuis
les arguments ordinaires. Le type de retour attendu ne participe pas à cette
inférence.

Un argument de type peut être lui-même une spécialisation générique, y compris
lorsqu'un callback nommé emploie le même type concret :

```sx
func passing(entry:@Entry<str, int>) bool {
    return entry.value >= 10
}

let explicit = count_where<Entry<str, int>>(entries, passing)
let inferred = count_where(entries, passing)
```

## Exiger un protocole

Ajoutez un protocole après `:` pour limiter les types acceptés :

```sx
func render<T:Drawable>(value:T) {
    value.draw()
}
```

La spécialisation vérifie que le type concret respecte nominalement le
protocole demandé. Cette contrainte est statique ; elle n'ajoute pas de
dispatch dynamique à l'exécution.

[Revenir aux fonctions](README.md) ·
[Définir un protocole](../Data-types/Protocols.md)
