# Écrire et lancer des tests

Un bloc `test` décrit un scénario indépendant. Placez-le à la racine d'un
fichier `.sx`, puis vérifiez le résultat attendu avec `assert` :

```sx
func double(value:int) int {
    return value * 2
}

test "doubler un entier" {
    assert(double(21) == 42)
}
```

Lancez tous les blocs du fichier avec :

```sh
silex test Tests/Numbers.sx
```

Une exécution réussie affiche le scénario puis un résumé :

```text
ok - doubler un entier
1 passed; 0 failed
```

La commande retourne un code différent de zéro lorsqu'un test échoue ou
qu'une source ne peut pas être compilée. Un échec arrête le bloc concerné,
mais les blocs suivants continuent de s'exécuter. Chaque scénario natif est
isolé dans son propre processus : un `panic` ou un arrêt par signal ne peut
donc pas interrompre toute la campagne.

## Tester tout un dossier

Passez un dossier pour découvrir récursivement les fichiers `.sx` qui
contiennent au moins un bloc `test` :

```sh
silex test Tests
```

Les fichiers sont exécutés dans l'ordre lexicographique. Les racines de
plateforme ou de cible qui ne correspondent pas à la machine actuelle sont
ignorées. Un dossier sans test réussit avec un rapport contenant zéro test.

Dans le rapport d'un dossier, chaque scénario est précédé du chemin relatif de
sa source :

```text
ok - Numbers.sx :: doubler un entier
```

Seuls les blocs du fichier physique sélectionné sont activés. Les tests placés
dans ses dépendances ne sont pas exécutés indirectement.

## Nommer et isoler les scénarios

La description est facultative. Un bloc anonyme est identifié par sa ligne :

```sx
test {
    assert(2 + 2 == 4)
}
```

Chaque bloc peut déclarer ses propres fonctions locales. Elles sont visibles
uniquement dans ce scénario et ne capturent pas ses variables : transmettez
leurs données par paramètres.

```sx
test "additionner deux valeurs" {
    func add(left:int, right:int) int {
        return left + right
    }

    assert(add(20, 22) == 42, "la somme attendue vaut 42")
}
```

Les blocs `test` n'appartiennent pas au programme ordinaire : `run`,
`interpret` et `compile` ne les ajoutent ni à l'IR ni à l'exécutable.

## Reproduire un test sans cache

Silex conserve ses artefacts de test privés sous `.silex/test`. Pour forcer
leur reconstruction, utilisez :

```sh
silex test Tests --nocache
```

`--emit-ir` écrit également l'IR de chaque source sélectionnée avant son
rapport. Ces deux options servent surtout au diagnostic ; la commande
ordinaire reste `silex test <fichier|dossier>`.

[Revenir aux outils](README.md) ·
[Exécuter, interpréter ou compiler un programme](Run-and-compile.md) ·
[Consulter la référence de la CLI](CLI-reference.md)
