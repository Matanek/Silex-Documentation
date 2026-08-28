# Protéger un état partagé

Un bloc `mutex` exécute une modification partagée en tenant l'unique verrou de
section critique du processus Silex.

```sx
func main() {
    var pending:int[] = []

    mutex {
        pending.append(42)
    }

    print(pending[0])
}
```

Le verrou est récursif : le code protégé peut appeler une fonction qui ouvre
elle-même un bloc `mutex`. Silex le libère à chaque sortie du bloc, y compris
avec `return`, `break`, `continue` ou la propagation récupérable de `try`.

Il n'existe volontairement aucune opération manuelle `lock` ou `unlock`. La
portée lexicale du bloc garantit que le verrou ne reste pas détenu après une
sortie anticipée.

[Revenir au contrôle de l'exécution](README.md) ·
[Retourner une erreur récupérable](../Functions/Result.md)
