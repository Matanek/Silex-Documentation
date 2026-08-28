# Échanger du texte et des scalaires avec C

Les pointeurs d'Interop sont des capacités éphémères valides seulement comme
arguments directs d'un appel étranger. Ils ne peuvent être stockés, retournés
ou conservés par l'API appelée.

## Lire les octets d'une chaîne

`C.pointer(text)` expose en lecture les octets UTF-8 de la chaîne pendant
l'appel. `C.byte_count(text)` renvoie leur nombre sous forme de `C.Size`, tandis
que `text.count()` compte les scalaires Unicode. `C.byte_at(text, index)` lit un
`uint8` et exige un indice inférieur au nombre d'octets.

Ces opérations peuvent aussi servir à un algorithme portable défini en octets,
sans aucune bibliothèque système.

Une API qui attend un UTF-8 terminé par zéro utilise
`C.terminated_pointer(text)`. Sa durée de vie reste celle de l'appel direct,
mais un octet zéro est garanti après la chaîne sans être inclus dans
`C.byte_count(text)`.

## Laisser le système écrire un scalaire

```sx
var seed:uint32 = 0
let written = getrandom(C.mutable_pointer(seed), 4 as C.Size, 0)
```

`C.mutable_pointer` accepte une variable stable entière ou flottante ainsi
qu'un tableau fixe de tels scalaires. L'adresse reste valide uniquement pour
l'appel étranger direct.

## Adapter un tampon d'octets

Une API système ne peut pas consommer directement une vue `uint8[..]` : les
éléments d'une collection Silex suivent une disposition privée, pas celle d'un
tableau C.

Un adaptateur de plateforme compacte d'abord la vue avec `C.string`, puis peut
exposer ce `var str` privé avec `C.mutable_string_pointer(buffer)` pendant un
appel. Après une écriture, il relit les octets avec `C.byte_at` et les copie
vers la vue publique modifiable.

Cette opération est réservée à un tampon de plateforme fraîchement alloué et
sans alias. Elle ne rend jamais les chaînes Silex ordinaires modifiables.

## Lire une disposition privée

`C.load<T>(address, byte_offset)` et
`C.store<T>(address, byte_offset, value)` lisent ou écrivent des scalaires
entiers ou flottants à un décalage explicite. `C.store` produit la valeur
écrite.

Ces primitives servent à des dispositions privées telles que `sockaddr`. Elles
ne rendent ni leur mémoire ni leur disposition publiques dans l'API du package.

[Revenir à l'interopérabilité](README.md) ·
[Fournir un callback](Callbacks.md)
