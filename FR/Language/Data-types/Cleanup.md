# Exécuter un nettoyage déterministe

Un bloc `drop` libère une ressource lorsque la valeur qui la possède termine sa
durée de vie normale.

## Nettoyer une structure

```sx
struct File {
    let descriptor:int

    drop {
        print("fermeture ", self.descriptor)
    }
}

func main() {
    {
        let file = File(descriptor:1)
    }
    print("nettoyage terminé")
}
```

`drop` s'exécute à la fin déterministe de chaque valeur complète qui n'a pas
été transférée. Il ne rend pas la structure non copiable : après
`var second = first`, `first` et `second` sont deux valeurs vivantes et chacune
exécute son propre bloc exactement une fois.

Une structure exécute son bloc avant de détruire ses champs. Ceux-ci sont
détruits dans l'ordre inverse de leur déclaration.

## Nettoyer une classe

```sx
class Connection {
    drop {
        print("fermée")
    }
}
```

Le bloc d'une classe s'exécute une fois lorsque la dernière racine atteignable
disparaît. Les cycles devenus inatteignables sont eux aussi finalisés. La
libération qui rend un composant inatteignable commence son nettoyage, puis les
champs possédés propagent le nettoyage à travers ce composant.

Avec l'héritage, les blocs s'exécutent depuis la classe dynamique vers ses
bases. `drop` n'est ni virtuel ni appelable et n'est suivi d'aucun appel
explicite à `super`.

## Nettoyer toutes les sorties normales

`return`, `break`, `continue` et la propagation de `try` nettoient chaque portée
qu'ils quittent. Une terminaison fatale avec `panic`, un échec d'assertion ou
une erreur d'exécution ne promet pas ce nettoyage.

[Revenir aux types de données](README.md) ·
[Copier ou transférer une valeur](../Ownership/Copy-and-move.md)
