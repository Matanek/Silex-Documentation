# Lier et appeler une fonction C

Un package importe `Interop.C` et `Interop.Boundary`, puis déclare au niveau du
module une liaison connue à la compilation :

```sx
use Interop.C
use Interop.Boundary

let write = C.function<
    func(int32, C.Pointer<uint8>, C.Size) C.SignedSize
>(
    library:Boundary.System,
    name:"write"
)
```

`System` est un fournisseur déclaré par le package pour chaque cible prise en
charge. Ce `let` ne crée aucun état global modifiable et n'exécute aucun
initialiseur. `C.function` applique la convention d'appel C de la cible.

Le nom reste celui documenté par l'API, ici `write`. Le fournisseur sélectionné
apporte les entrées de lien sans les exposer dans l'API Silex publique.

## Appeler comme une fonction ordinaire

```sx
func write_text(text:str) C.SignedSize {
    return write(
        1,
        C.pointer(text),
        C.byte_count(text)
    )
}
```

Le résultat brut suit le contrat C. Le binding doit gérer les écritures
partielles, les erreurs système et leur conversion vers son type d'erreur
public.

Une fonction C sans résultat emploie `void`. L'appeler comme instruction ne
crée aucune valeur Silex cachée. Les paramètres et résultats octet non signés
emploient `uint8` exactement ; déclarer en `uint32` un résultat C de type
`Boolean` huit bits ne respecte pas l'ABI.

La surface acceptée reste volontairement étroite : entiers et flottants de
largeur explicite, `C.Pointer<T>`, `C.MutablePointer<T>` et `void` en retour.
Les détails de mémoire sont regroupés dans
[les échanges directs](Memory.md).

[Revenir à l'interopérabilité](README.md) ·
[Connaître les limites](Limits.md)
