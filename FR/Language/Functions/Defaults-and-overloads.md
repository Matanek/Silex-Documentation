# Adapter un appel avec des valeurs par défaut et des surcharges

Une valeur par défaut rend les derniers arguments facultatifs. Une surcharge
permet à un même nom de choisir plusieurs comportements selon les types reçus.

## Déclarer des valeurs par défaut

```sx
func greet(message:str = "Bonjour", repetitions:int = 1) {
    print(message, " x", repetitions)
}

func main() {
    greet()
    greet("Salut")
    greet(repetitions:2)
}
```

Les paramètres avec valeur par défaut forment un suffixe continu de la
déclaration. Un appel nommé peut omettre l'un d'eux tout en fournissant un
paramètre suivant. Chaque expression omise est évaluée au site d'appel, pas au
moment où la fonction est déclarée.

Dans un appelable générique, une valeur par défaut est vérifiée avec les types
concrets seulement lorsqu'un appel l'omet. Une spécialisation peut donc fournir
explicitement un argument même si la valeur par défaut déclarée ne correspond
pas à ce type concret.

## Déclarer des surcharges

```sx
func describe(value:int) str { return "entier" }
func describe(value:str) str { return "chaîne" }
```

Les types des paramètres distinguent les surcharges. Le type de retour et les
noms des paramètres ne les distinguent pas. Deux déclarations ne peuvent pas
exposer le même préfixe d'appel à travers leurs valeurs par défaut.

Les paramètres correspondants d'une famille de surcharges doivent porter les
mêmes noms : une étiquette ne choisit jamais une surcharge. Le compilateur
préfère la surcharge qui demande les conversions implicites les moins
coûteuses.

Lorsqu'un entier peut alimenter une surcharge `float` et une surcharge
`float64`, Silex préfère `float`, c'est-à-dire `float32`. Une valeur déjà typée
`float64` sélectionne la surcharge `float64`. Cette règle vaut pour les appels
positionnels comme pour les appels nommés.

Une surcharge concrète applicable a priorité sur l'inférence d'une fonction
générique.

[Revenir aux fonctions](README.md) ·
[Adapter une fonction à plusieurs types](Generics.md)
