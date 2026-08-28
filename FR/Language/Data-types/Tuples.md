# Regrouper des valeurs avec un tuple

Un tuple nommé convient à un petit résultat dont les rôles sont stables mais
qui ne justifie pas une structure nominale.

```sx
func size() (width:int, height:int) {
    return (width:1280, height:720)
}

func main() {
    let value = size()
    print(value.width)
    print(value.height)
}
```

Les noms déclarés appartiennent au type du tuple. La construction les répète
tous dans l'ordre de déclaration, ce qui empêche d'inverser silencieusement
deux valeurs du même type. L'éditeur peut proposer ces membres nommés.

## Employer un tuple positionnel

```sx
func bounds() (int, int) {
    return (0, 100)
}

let (minimum, maximum) = bounds()
```

Un tuple nommé peut être déstructuré dans le même ordre. La déstructuration
lie exactement un nom par élément. Les tuples positionnels n'exposent aucun
membre nommé.

## Composer un type tuple

Un tuple contient au moins deux éléments, peut mélanger les types et peut être
imbriqué, rangé, passé, retourné ou utilisé comme élément de collection :

```sx
let samples:(int, bool)[2] = [(1, true), (2, false)]
let nested:((x:int, y:int), bool) = ((x:10, y:20), true)
```

Une expression unique entre parenthèses reste un simple groupement, jamais un
tuple à un élément. Les éléments d'un tuple sont immuables. Copie, transfert,
durée de vie et destruction s'appliquent récursivement à leurs valeurs.

Une fonction renvoie toujours une valeur unique : cette valeur peut être un
tuple, mais le tuple n'introduit pas un mécanisme séparé de retours multiples.
Sa disposition mémoire, son alignement et sa convention de retour restent des
détails du compilateur et ne sont pas disponibles dans les déclarations C.

## Décrire un motif d'accès emprunté

`@` et `&` peuvent annoter les éléments d'un tuple lorsqu'une API générique le
consomme explicitement comme motif non stockable. GFX emploie par exemple :

```sx
ECS.Query<(@Velocity, &Transform)>
```

Ce motif n'est pas une valeur tuple : il ne peut être construit, stocké,
retourné ou déstructuré indépendamment. L'API consommatrice crée les emprunts
lexicaux à chaque itération ; les annotations restent des intentions de lecture
et de modification, jamais des références stockables.

[Revenir aux types de données](README.md) ·
[Emprunter une valeur](../Ownership/References.md)
