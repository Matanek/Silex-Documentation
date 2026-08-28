# Parcourir des valeurs

La liaison d'une boucle `for` indique si chaque élément est emprunté, copié ou
réécrit dans sa collection.

## Lire chaque élément

```sx
func main() {
    let values = [10, 20, 30]

    for value in values {
        print(value)
    }
}
```

Sans modificateur, la boucle emprunte l'élément en lecture pendant cette
itération. `for let value in values` crée au contraire une copie immuable.

## Modifier les éléments stockés

```sx
for var value in values {
    value += 1
}
```

`for var` réécrit la valeur finale avant de passer à l'élément suivant, avant
un `continue` et avant un `break`. La collection doit être nommée et modifiable.

## Lire aussi l'indice

```sx
for index, item in values.indexed() {
    print("$(index): $(item)")
}
```

L'indice est un `int` immuable qui commence à `0` et suit l'ordre de la
collection. L'élément conserve les modes ordinaires : `for index, let item`
crée une copie, tandis que `for index, var item` réécrit l'élément stocké. Le
récepteur est évalué une fois et une collection vide n'exécute aucun corps.

Cette forme à deux liaisons appartient uniquement à `indexed()` ; elle ne
déstructure ni un tuple arbitraire ni une autre source de boucle.

## Parcourir du texte Unicode

Une chaîne se parcourt directement en scalaires Unicode `uint32` :

```sx
for scalar in "A🙂é" {
    print(scalar)
}
```

La boucle visite `65`, `128578`, puis `233`, conformément à `str.count()`.
Elle décode l'UTF-8 à la demande sans construire de liste. Un scalaire n'est ni
un octet encodé ni forcément un graphème visible : employez
`STD.Text.UTF8.bytes(text)` pour les octets d'un protocole et
`STD.Text.Grapheme` pour les unités visibles par l'utilisateur.

Une chaîne n'accepte ni `for var` ni `indexed()` direct. Demandez une vue STD
explicite lorsque les positions font partie de l'opération.

## Parcourir une plage

`0...3` et `range(3, 0)` excluent leur borne de fin. Les bornes sont évaluées
une seule fois, de gauche à droite ; deux bornes égales n'exécutent aucune
itération. Les exemples détaillés se trouvent avec
[les boucles](../Control-flow/Conditions-and-loops.md).

## Parcourir un curseur applicatif

Tout type qui expose exactement une méthode d'instance visible `next() T?`
peut servir directement de source :

```sx
struct Countdown {
    var current:int

    func next() int? {
        if self.current == 0 { return null }
        let value = self.current
        self.current--
        return value
    }
}

for value in Countdown(current:3) {
    print(value)
}
```

La source est évaluée une fois et copiée dans un curseur privé modifiable.
Écrivez `move cursor` pour transférer explicitement un curseur existant.
`next()` est appelé une fois par tentative ; une valeur présente exécute le
corps et `null` arrête la boucle.

Si `next` est surchargée, Silex retient les formes appelables sans argument
explicite et retournant `T?` ; exactement une doit subsister. La liaison sans
modificateur emprunte la valeur produite pour le corps, et `for let` en crée une
copie indépendante.

`for var`, `indexed()` et la forme à deux liaisons sont indisponibles : une
valeur produite n'est pas le stockage modifiable d'une collection, et le
curseur ne promet ni indice ni taille connue.

[Revenir aux collections](README.md) ·
[Emprunter une vue](Views.md)
