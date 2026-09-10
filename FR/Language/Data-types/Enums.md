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

Une expression entre parenthèses reste une branche concise, même lorsqu'elle
occupe plusieurs lignes :

```sx
let predicted = match condition {
    true => (x * 10.0 / 2.0 + 0.5)
    else => 0.0
}
```

## Choisir selon des conditions ordonnées

Omettez le sujet pour écrire une suite de conditions. Le compilateur les
évalue de haut en bas et sélectionne la première qui produit `true` :

```sx
let category = match {
    score >= 16 => "excellent"
    score >= 10 => "admis"
    else => "insuffisant"
}
```

Chaque condition doit produire un `bool`. Cette forme exige toujours `else`,
qui garantit une valeur ou une action lorsque toutes les conditions échouent.

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

Lorsqu'un `match` est une instruction, une branche courte peut aussi appeler
directement une fonction :

```sx
match condition {
    true => accept()
    else => reject()
}
```

Une branche expression d'un `match` instruction doit être un appel, une
cascade, une propagation avec `try` ou un autre `match`. Les blocs et ces
branches courtes peuvent être mélangés et ne continuent jamais implicitement
vers la branche suivante.

## Calculer une valeur dans un bloc

Terminez un bloc de branche par `yield` lorsque son résultat demande plusieurs
instructions :

```sx
let predicted = match condition {
    true => {
        var result = 0.0
        result += x * 10.0
        result /= 2.0
        yield result + 0.5
    }
    else => 0.0
}
```

Dans un `match` utilisé comme valeur, chaque bloc de branche doit se terminer
directement par `yield expression`. Une branche expression voisine reste
valide ; toutes les valeurs produites doivent avoir exactement le même type.
`return` termine toujours la fonction englobante, tandis que `yield` fournit
la valeur de la branche au `match` producteur le plus proche.

Les `match` peuvent donc être imbriqués sans ambiguïté :

```sx
let value = match outer {
    true => {
        yield match inner {
            true => { yield 1 }
            else => 0
        }
    }
    else => -1
}
```

Le `yield 1` appartient au `match inner`; le `yield match inner` fournit ensuite
son résultat au `match outer`.

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
