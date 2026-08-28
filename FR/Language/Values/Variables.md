# Conserver et modifier des valeurs

Un programme donne un nom aux valeurs qu'il utilise. Certaines ne changeront
pas ; d'autres doivent évoluer.

## Choisir entre `let` et `var`

Ce programme conserve le nom du langage et met à jour un compteur de leçons :

```sx
func main() {
    let language:str = "Silex"
    var lessons = 1

    lessons = 2

    print(language)
    print(lessons)
}
```

`language` conserve la même valeur. `lessons` commence à `1`, puis reçoit la
valeur `2`.

Utilisez `let` lorsque le nom gardera la valeur reçue à sa déclaration. Une
nouvelle affectation à ce nom sera refusée par le compilateur.

Utilisez `var` lorsque le programme doit lui attribuer une autre valeur. Ici,
la ligne `lessons = 2` exige donc que `lessons` soit déclaré avec `var`.

Commencez par `let` et passez à `var` seulement lorsqu'une modification est
nécessaire. L'intention de votre code reste ainsi visible dès la déclaration.

## Indiquer ou laisser déduire le type

Une annotation placée après le nom fixe explicitement le type, comme dans
`let language:str = "Silex"`.

Sans annotation, le compilateur déduit le type depuis la première valeur.
Dans l'exemple, il déduit que `lessons` est un `int` à partir de `1`.

Pour comparer les types disponibles et choisir une largeur précise, consultez
[les types intégrés de Silex](Built-in-types.md).

## Modifier un champ

La racine et chaque champ traversé doivent être modifiables :

```sx
struct Position {
    var x:int
    var y:int
}

func main() {
    var position = Position(x:1, y:2)
    position.x = 10
    print(position.x)
}
```

Un champ déclaré avec `let`, ou un `let` rencontré avant lui sur le chemin,
rend la suite du chemin accessible en lecture seule.

## Signaler un état partagé

Une liaison capable d'atteindre une instance de classe se déclare avec `var`
lorsque cette instance peut changer :

```sx
var player = Player()
player.damage(10)
```

Même si `player` continue de désigner la même instance, `var` indique ici que
l'état partagé qu'il permet d'atteindre peut être modifié. Le fonctionnement
des instances est détaillé avec [les classes](../Data-types/Classes.md).

## Respecter la portée lexicale

Un nom existe seulement dans le bloc où il est déclaré et dans ses blocs
enfants. Un paramètre ou une variable locale visible ne peut pas être redéclaré
avec le même nom. Deux branches sœurs peuvent en revanche employer chacune un
nom local identique, car leurs portées ne se chevauchent pas.

[Revenir aux valeurs](README.md) ·
[Choisir un type intégré](Built-in-types.md)
