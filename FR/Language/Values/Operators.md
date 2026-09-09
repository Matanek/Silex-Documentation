# Calculer, comparer et enchaîner des opérations

Les opérateurs Silex couvrent les calculs numériques et peuvent recevoir une
sémantique explicite pour les types d'un package. Les opérations impossibles
restent des erreurs de compilation.

## Calculer avec des nombres

```sx
func main() {
    let left = 10
    let right = 3

    print(left + right)
    print(left - right)
    print(left * right)
    print(left / right)
    print(left % right)
}
```

Ce programme affiche `13`, `7`, `30`, `3` et `1`, chacun sur sa propre ligne.
Les opérateurs `+`, `-`, `*` et `/` acceptent les nombres ; `%` accepte
seulement les entiers. Silex contrôle les dépassements, la division par zéro et
la négation qui ne peut pas être représentée.

Les entiers compatibles sont élargis dans leur famille signée ou non signée.
La présence d'un nombre à virgule sélectionne le type commun `float32` ou
`float64`.

## Définir un opérateur pour un type

Une fonction `operator` se déclare au niveau du module. Ses paramètres rendent
les deux opérandes visibles et son corps reste une fonction Silex ordinaire :

```sx
struct Vec2 {
    var x:float
    var y:float
}

func operator +(left:Vec2, right:Vec2) Vec2 {
    return Vec2(left.x + right.x, left.y + right.y)
}

func operator *(left:Vec2, right:float) Vec2 {
    return Vec2(left.x * right, left.y * right)
}

func operator -(value:Vec2) Vec2 {
    return Vec2(-value.x, -value.y)
}
```

`+`, `*` et `/` demandent deux paramètres. `-` accepte soit deux paramètres
pour la soustraction, soit un seul pour la négation. Chaque orientation est
explicite : accepter `Vec2 * float` ne déclare pas automatiquement
`float * Vec2`.

Les paramètres sont des valeurs possédées, sans valeur par défaut, et le type
de retour est une valeur possédée non vide. Au moins un opérande doit être un
type nominal déclaré par le package de la fonction ; un package ne peut donc
pas redéfinir les calculs entre deux types étrangers. Les opérateurs génériques
ne sont pas encore acceptés.

La visibilité suit celle des fonctions. Une surcharge destinée aux utilisateurs
d'un package doit être `public` et devient disponible avec l'import qui expose
son module. La résolution compare les types des opérandes et leurs conversions
implicites ; le type de retour ne choisit jamais une surcharge.

## Comparer des valeurs

Les nombres reconnaissent `==`, `!=`, `<`, `<=`, `>` et `>=`. Les chaînes
comparent leurs octets UTF-8 exacts. Deux structures sont comparables lorsque
tous leurs champs le sont ; leur comparaison est récursive. Les enums
comparent leur variante active puis leurs valeurs associées. Les classes
comparent leur identité partagée, pas le contenu de leurs champs.

Comparer `T?` avec une valeur `T` promeut cette dernière en optionnel. Ainsi,
`maybe_name == "Silex"` vérifie à la fois la présence de la chaîne et son
contenu ; une absence produit `false`.

## Combiner des conditions

```sx
if ready && count > 0 {
    work()
}

if missing || expired {
    refresh()
}
```

`&&` et `||` court-circuitent l'évaluation : leur opérande droit est évalué
seulement si le résultat n'est pas déjà connu. `!` inverse un booléen.

## Manipuler les bits non signés

`&`, `^`, `<<` et `>>` acceptent des entiers non signés. Le nombre de
positions d'un décalage doit tenir dans la largeur de l'opérande gauche.

```sx
let masked = flags & mask
let toggled = flags ^ mask
let shifted = value << 2
let reduced = value >> 1
```

## Modifier une valeur

Une variable, un champ ou un élément indexé modifiable accepte `+=`, `-=`,
`*=`, `/=`, `%=` ainsi que `++` et `--`. Chaque affectation composée effectue
la même opération contrôlée que son opérateur, puis range le résultat à
l'emplacement d'origine. Lorsqu'un opérateur binaire est surchargé, `+=`, `-=`,
`*=` ou `/=` réemploie automatiquement cette surcharge ; son résultat doit être
assignable au type de la cible. `%=` reste réservé aux entiers, et `++` et `--`
aux nombres.

## Appliquer plusieurs opérations au même objet

L'opérateur de cascade `..` évalue son récepteur une seule fois, lui applique
chaque segment et produit ce même récepteur après ses modifications :

```sx
var values:int[] = []
    ..append(10)
    ..append(20)
    ..reverse()
```

Un segment est un appel de méthode ou l'affectation directe d'un champ. Les
règles ordinaires de mutabilité, de visibilité, de possession et d'emprunt
continuent de s'appliquer. Une valeur existante doit donc être modifiable si un
segment écrit dedans ; un nouveau temporaire possédé peut être configuré
directement.

Un point simple après un appel termine la cascade et reprend l'accès ordinaire
sur le résultat de cet appel : `values..append(30).count()`. `..` est un seul
token et reste distinct de `...`, qui forme une plage entière.

La priorité exacte des opérateurs est disponible dans la
[référence syntaxique](../../Reference/Syntax.md).

[Revenir aux valeurs](README.md) ·
[Choisir une branche ou une boucle](../Control-flow/Conditions-and-loops.md)
