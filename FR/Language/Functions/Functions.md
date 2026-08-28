# Définir et appeler une fonction

Une fonction nomme un comportement, déclare les valeurs qu'elle reçoit et
indique le type qu'elle renvoie.

```sx
func add(left:int, right:int) int {
    return left + right
}

func main() {
    print(add(20, 22))
}
```

Le type placé après les paramètres est le type de retour. Omettez-le lorsque
la fonction ne renvoie aucune valeur :

```sx
func greet(name:str) {
    print("Bonjour, ", name)
}
```

## Étiqueter les arguments

Un appel peut suivre l'ordre des paramètres ou employer leurs noms comme
étiquettes :

```sx
draw(hero, position)
draw(at:position, sprite:hero)
draw(hero, opacity:0.8, at:position)
```

Les arguments nommés peuvent apparaître dans n'importe quel ordre. Des
arguments positionnels peuvent les précéder, mais aucun argument positionnel
ne peut suivre le premier argument nommé. Chaque paramètre est fourni au plus
une fois.

Après l'association des étiquettes, Silex évalue les expressions et les passe
dans l'ordre de déclaration des paramètres. Les appels effectués à travers une
valeur de fonction ou un callback restent toujours positionnels.

Le nom d'un paramètre public appartient donc à l'interface source : le renommer
peut casser les appels nommés sans changer son type.

## Retourner chaque chemin

```sx
func sign(value:int) str {
    if value < 0 {
        return "négatif"
    }
    return "positif"
}
```

Une fonction qui renvoie autre chose que `void` doit retourner une valeur ou
terminer le programme sur chaque chemin atteignable.

[Revenir aux fonctions](README.md) ·
[Proposer des valeurs par défaut](Defaults-and-overloads.md)
