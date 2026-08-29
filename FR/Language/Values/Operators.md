# Calculer, comparer et enchaîner des opérations

Les opérateurs Silex conservent le type de leurs valeurs et signalent les
opérations numériques impossibles au lieu de laisser un résultat
silencieusement incorrect.

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

## Modifier une valeur numérique

Une variable, un champ ou un élément indexé modifiable accepte `+=`, `-=`,
`*=`, `/=`, `%=` ainsi que `++` et `--`. Chaque affectation composée effectue
la même opération contrôlée que son opérateur, puis range le résultat à
l'emplacement d'origine. `%=` reste réservé aux entiers.

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
