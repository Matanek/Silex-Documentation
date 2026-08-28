# Afficher un résultat ou interrompre le programme

Silex fournit trois effets directs pour observer ou arrêter un programme :
`print`, `assert` et `panic`.

## Afficher des valeurs

```sx
func main() {
    let x = 10
    let y = 5
    print("x=", x, ", y=", y)
}
```

`print` évalue ses arguments de gauche à droite, ne place aucun séparateur
entre eux et ajoute un unique retour à la ligne final. Il accepte les chaînes,
les nombres et les booléens. Le programme précédent affiche :

```text
x=10, y=5
```

L'[interpolation de chaîne](Values/Strings.md) permet de préparer le même texte
avant l'appel lorsque le résultat doit être réutilisé.

## Vérifier une condition

```sx
assert(count > 0, "count doit être positif")
```

Si la condition est fausse, `assert` écrit un diagnostic localisé dans la
source sur la sortie d'erreur, puis termine le programme avec le statut `1`.
Une condition vraie ne produit rien et laisse l'exécution continuer.

## Arrêter avec un message

```sx
panic("état impossible")
```

`panic` écrit toujours un diagnostic localisé dans la source sur la sortie
d'erreur et termine avec le statut `1`. Utilisez plutôt
[un `Result`](Functions/Result.md) lorsque l'appelant doit pouvoir récupérer
l'échec.

[Revenir aux concepts du langage](README.md)
