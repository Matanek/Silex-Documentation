# Relier un package à une API système

`Interop` est la frontière bas niveau utilisée pour construire des bindings
Silex. Une application dépend normalement d'un package portable tel que STD et
ne manipule jamais directement cette surface.

Un package nommé possède chaque fournisseur étranger qu'il utilise. Son
manifeste ciblé peut sélectionner une archive précompilée, des bibliothèques
système nommées, des frameworks Apple ou une combinaison de ces entrées. Un
fournisseur qui appelle seulement la plateforme n'a besoin d'aucune archive
factice. Il reste privé au package déclarant.

Le compilateur valide si les scalaires et pointeurs déclarés peuvent franchir
l'ABI de la cible. Il ne maintient aucun catalogue de symboles de packages :
ajouter une fonction dont la forme ABI est déjà prise en charge modifie seulement
le source du package et, si nécessaire, son manifeste.

- [Lier et appeler une fonction C](Functions.md)
- [Échanger du texte, des scalaires et des structures privées](Memory.md)
- [Fournir un callback ou appeler une adresse](Callbacks.md)
- [Connaître la frontière actuellement prise en charge](Limits.md)

Les anciens espaces `MacOS`, `Linux` et `Windows` restent des contrats de
compatibilité de la toolchain. Les nouveaux bindings de package utilisent
`Boundary.<Provider>`.

[Revenir aux concepts du langage](../README.md)
