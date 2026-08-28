# Représenter une valeur absente

Ajoutez `?` à un type valeur pour représenter soit `null`, soit une valeur de
ce type.

```sx
struct Position {
    var x:int
}

func main() {
    let count:int?
    let title:str? = "Silex"
    var position:Position? = null

    print(count == null)
    print(title!)
    print(position == null)
}
```

Une valeur `T` est promue automatiquement vers `T?`, mais son extraction n'est
jamais implicite. Le littéral `null` demande un type optionnel attendu.

## Composer les suffixes

Les suffixes de type se composent de gauche à droite. `Position?[]` est une
liste de positions optionnelles ; `Position[]?` est une liste elle-même
optionnelle. `int?[3]` est un tableau fixe de trois entiers optionnels.

Les suffixes optionnels peuvent se répéter. `T??` signifie `(T?)?` : `null`
initialise la couche extérieure absente, promouvoir un `T?` ajoute une couche
extérieure présente et promouvoir un `T` rend toutes les couches présentes.
Une liaison conditionnelle et le suffixe `!` retirent une seule couche à la
fois ; `value!!` est donc explicite lorsque deux couches doivent être forcées.

## Prouver la présence d'une variable locale

```sx
if position != null {
    print(position.x)
}

if position == null {
    print("absente")
} else {
    print(position.x)
}
```

La preuve appartient à cette variable et à cette branche. Réaffecter un `var`
invalide la preuve pour les instructions suivantes.

## Lier directement la valeur présente

```sx
if position = find_position() {
    print(position.x)
}

while var item = next_item() {
    item.advance()
}
```

Sans modificateur, la liaison locale est immuable ; `let` explicite le même
comportement et `var` crée une copie locale modifiable. La source est évaluée
une fois à chaque tentative.

## Accéder sans forcer la présence

```sx
let x:int? = profile?.position?.x
position?.translate(3)
```

Chaque étape optionnelle demande son propre `?.`. Les arguments d'une méthode
sûre sont évalués seulement lorsque le récepteur est présent. Un appel sûr qui
modifie la valeur exige un emplacement optionnel `var`.

Une affectation peut elle aussi traverser un chemin optionnel :

```sx
profile?.position?.x = 10
statistics?.accepted += 1
state?.values[index] = value
```

La racine doit être `var`. Silex évalue le chemin de gauche à droite et s'arrête
sans effet au premier récepteur absent. Les indices et la valeur de droite sont
évalués seulement après la présence de tous les segments précédents.
L'affectation simple, l'affectation composée, `++` et `--` partagent ce
court-circuit. L'instruction ne produit aucune valeur.

## Forcer une valeur présente

```sx
let configuration = load_configuration()!
print(configuration.name)
```

Le suffixe `!` évalue son opérande une fois et enlève exactement une couche
optionnelle. Une absence arrête l'exécution avec l'erreur localisée
`forced optional extraction failed`. Le préfixe `!value` reste la négation
booléenne ; `value!` est l'assertion optionnelle.

## Choisir une valeur de remplacement

```sx
let display_name = declaration.alias ?? declaration.name
let port = configured_port() ?? default_port()
```

`??` évalue son opérande gauche optionnel une seule fois. Une valeur présente
est renvoyée sans évaluer la droite ; une absence évalue le remplacement. Un
remplacement `T` produit un `T`, tandis que `T? ?? T?` reste optionnel.
L'opérateur s'associe à droite et possède une priorité plus faible que les
opérateurs logiques et arithmétiques.

[Revenir aux types de données](README.md) ·
[Choisir avec un enum et `match`](Enums.md)
