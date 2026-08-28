# Fournir un callback ou appeler une adresse C

Une API système peut appeler une fonction Silex nommée ou fournir elle-même une
table de fonctions.

## Exposer une fonction nommée

`C.function_address(callback)` renvoie l'adresse d'entrée d'une fonction
concrète. Une fonction générique est spécialisée explicitement avec
`C.function_address<T...>(callback)`.

`C.object_address(value)` et `C.object_from_address<T>(address)` transportent
l'identité d'une classe à travers le contexte opaque d'un callback système.
L'objet doit rester vivant jusqu'à ce que le système ait fini d'utiliser ce
contexte.

Ces opérations sont réservées aux adaptateurs privés. Elles ne transforment pas
les adresses en valeurs publiques du langage.

Sur Windows X64, l'adresse de fonction pointe vers un adaptateur Win64 généré.
Il traduit les registres du callback vers la convention interne portable et
préserve les registres non volatils Win64. Les appels ordinaires à travers une
valeur de fonction utilisent le même adaptateur dans l'autre sens ; le callback
reste donc appelable par le système comme par Silex.

## Appeler une entrée de table

```sx
let method = C.load<uint>(vtable, 24 as uint)
let result = C.call<func(uint, int32) int32>(method, object, value)
```

`C.call<func(...) T>` applique l'ABI C de la cible à une adresse de fonction
`uint`. Ses paramètres et son résultat partagent la surface étroite de
`C.function` : scalaires entiers ou flottants, `C.Pointer<T>`,
`C.MutablePointer<T>` et `void` en retour. La signature est vérifiée à la
compilation.

Résoudre la table, maintenir son propriétaire en vie et valider l'adresse
restent entièrement la responsabilité de l'adaptateur.

[Revenir à l'interopérabilité](README.md) ·
[Connaître les limites](Limits.md)
