# Choisir une branche et répéter une action

Les conditions Silex sont toujours des valeurs `bool`. Elles peuvent choisir
une branche avec `if`, contrôler une boucle `while` ou filtrer les itérations
d'une boucle `for`.

## Choisir une branche

```sx
func main() {
    let value = 0

    if value < 0 {
        print("négatif")
    } elif value == 0 {
        print("zéro")
    } else {
        print("positif")
    }
}
```

`else if` est une autre graphie acceptée de `elif`. La première condition vraie
sélectionne sa branche ; `else` reçoit le cas restant.

## Répéter tant qu'une condition est vraie

```sx
var remaining = 3

while remaining > 0 {
    print(remaining)
    remaining--
}
```

La condition est réévaluée avant chaque itération. Une condition fausse dès le
départ n'exécute jamais le corps.

## Interrompre ou ignorer une itération

`break` quitte la boucle la plus proche. `continue` abandonne seulement
l'itération courante et reprend au prochain test de cette même boucle.

```sx
while ready() {
    if finished() {
        break
    }
    if ignored() {
        continue
    }
    work()
}
```

## Parcourir une plage entière

```sx
for index in 0...3 {
    print(index)
}

for index in range(3, 0) {
    print(index)
}
```

Les deux formes excluent la borne de fin. `0...3` produit `0`, `1`, puis `2` ;
`range(3, 0)` produit `3`, `2`, puis `1`. Deux bornes égales ne produisent
aucune itération.

Pour parcourir une collection, consultez
[les formes d'itération](../Collections/Iteration.md).

[Revenir au contrôle de l'exécution](README.md) ·
[Protéger un état partagé](Mutex.md)
