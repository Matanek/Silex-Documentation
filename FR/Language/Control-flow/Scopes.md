# Limiter explicitement une portée

Un bloc nu crée une portée lexicale anonyme. Il s'exécute exactement une fois
et ne produit aucune valeur.

```sx
struct Counter {
    var value:int
    func increment() { self.value++ }
}

func main() {
    var counter = Counter(value:0)
    {
        let increment:func() = counter.increment
        increment()
    }

    counter.value += 1
    print(counter.value)
}
```

La portée libère ici l'emprunt conservé par la méthode liée avant l'accès
direct suivant à `counter`. Ses variables disparaissent après `}` et leur
nettoyage s'exécute avant toute sortie normale ou transférée du bloc.

Deux portées successives peuvent réutiliser les mêmes noms locaux. Le bloc ne
porte aucun point-virgule final. Il n'intercepte pas le contrôle : `break` et
`continue` ciblent toujours la boucle englobante la plus proche, tandis que
`return` quitte toujours la fonction.

[Revenir au contrôle de l'exécution](README.md) ·
[Comprendre les emprunts des callbacks](../Functions/Callbacks.md)
