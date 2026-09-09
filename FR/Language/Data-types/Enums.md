# Sélectionner une valeur avec `match`

Un enum déclare un ensemble fermé de variantes. Une variante peut transporter
des valeurs associées.

```sx
enum Connection {
    waiting
    connected(str)
    closed(str)
}

func main() {
    let pending = Connection.waiting
    let active = Connection.connected("serveur")
    print(pending != active)
}
```

Une variante sans valeur associée est déjà une valeur et n'a pas besoin de
parenthèses. L'ancienne forme `Connection.waiting()` reste acceptée. Une
variante avec valeurs reste une construction et exige ses parenthèses.

Le mot `in` est contextuel dans les déclarations d'enum, les accès de variantes
et les branches de `match`. Un nom intentionnel comme `Easing.in` reste donc
disponible.

## Produire une valeur avec `match`

```sx
func describe(connection:Connection) str {
    return match connection {
        waiting => "attente"
        connected(name) => name
        closed(reason) => reason
    }
}
```

Le sujet est évalué une fois et n'est pas consommé. Chaque variante apparaît
exactement une fois et chaque branche produit exactement le même type.

Écrivez `_` pour ignorer volontairement une valeur associée. Il occupe toujours
sa position mais ne déclare aucune variable ; la valeur reste possédée par
l'enum et suit sa durée de vie ordinaire. `let _` et `var _` sont invalides.
Seule une branche `else` absorbe toutes les variantes restantes.

## Filtrer une variante avec une garde

```sx
let category = match token {
    integer(value, _) if value < 0 => "négatif"
    integer(value, _) if value == 0 => "zéro"
    integer(_, _) => "positif"
    identifier(name, _) if name == "self" => "réservé"
    identifier(_, _) => "nom"
    else => "autre"
}
```

La garde s'exécute seulement après la correspondance de sa variante, voit les
liaisons du motif et doit produire un `bool`. Une garde fausse poursuit avec la
branche suivante.

Une branche gardée ne prouve jamais l'exhaustivité : chaque variante demande
finalement une branche non gardée, sauf si `else` couvre les cas restants. Une
branche non gardée rend inatteignable toute branche ultérieure de la même
variante.

## Exécuter des instructions dans les branches

```sx
match connection {
    waiting => { print("attente") }
    connected(name) => { print(name) }
    closed(reason) => { print(reason) }
}
```

Les branches en bloc et les branches expression ne peuvent pas être mélangées.
Une branche peut contenir un autre `match`, y compris une expression `match`
dont le résultat devient la valeur de la branche englobante. Chaque niveau
conserve ses propres liaisons et règles d'exhaustivité.

## Sélectionner un littéral scalaire

`match` accepte aussi un sujet `bool`, entier ou `str`. Chaque motif est un
littéral du même type que le sujet ; un littéral entier reçoit le type entier
du sujet et doit tenir dans son intervalle.

```sx
func status(code:int) str {
    return match code {
        200 => "ok"
        404 => "introuvable"
        else => "autre"
    }
}
```

Les branches sont essayées dans leur ordre et la première correspondance dont
la garde réussit est sélectionnée. Le sujet est évalué une seule fois. Une
branche non gardée rend inatteignable tout motif identique placé après elle ;
deux branches non gardées ne peuvent pas répéter le même littéral.

Un `match` entier ou `str` exige une branche `else`. Un `match` booléen peut
l'omettre lorsque `true` et `false` possèdent chacun une branche non gardée.
Les branches ne continuent jamais implicitement dans la suivante : utilisez
un bloc dans chaque branche pour exécuter des instructions.

## Associer une valeur brute

```sx
enum Direction:int {
    north = 1
    south = -2
}

let code:int = Direction.north.raw_value
```

Un enum brut emploie `int` ou `str`. Chaque variante fournit un littéral unique.
L'enum et son type brut ne se convertissent jamais implicitement l'un vers
l'autre.

## Comparer et copier

`==` compare d'abord la variante active, puis récursivement toutes ses valeurs
associées. Deux variantes différentes sont inégales ; `!=` inverse ce résultat.
Les valeurs associées doivent elles-mêmes être comparables : les structures
comparent leurs champs et les classes leur identité partagée. Un enum brut
compare toujours sa variante, pas implicitement sa `raw_value`.

La copie d'un enum copie compositionnellement le contenu de sa variante active.
Si ce contenu possède un `drop`, chaque copie possède son propre contenu et
détruit seulement la variante active.

Les enums génériques sont traités avec
[les types génériques](Generics.md).

[Revenir aux types de données](README.md) ·
[Retourner une erreur récupérable](../Functions/Result.md)
