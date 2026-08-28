# Donner un autre nom à un type

Un alias donne un nom local plus parlant à un type existant. Il améliore le
vocabulaire du code sans créer un nouveau type.

## Déclarer un alias

Placez le nom existant après `use`, puis le nom local après `as` :

```sx
use int as Count

func main() {
    let total:Count = 3
    print(total)
}
```

Le programme affiche :

```text
3
```

Ici, `Count` et `int` désignent exactement le même type. Une valeur `Count`
peut donc être utilisée partout où un `int` est attendu, sans conversion.

## Raccourcir un nom qualifié

Un alias peut aussi raccourcir un nom provenant d'un autre module :

```sx
use Geometry.Vector as Vector
```

Dans ce fichier, `Vector` désigne alors `Geometry.Vector`. L'alias ne déplace
pas la déclaration d'origine et ne crée ni enveloppe ni identité de type
supplémentaire.

Choisissez un alias lorsque le nouveau nom exprime mieux le rôle du type ou
évite de répéter un long chemin. Si deux valeurs doivent devenir
incompatibles malgré une représentation identique, un alias ne suffit pas :
il faut déclarer un véritable type.

[Revenir aux types de données](README.md) ·
[Choisir un type intégré](../Values/Built-in-types.md)
