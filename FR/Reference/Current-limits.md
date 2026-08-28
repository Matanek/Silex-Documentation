# Connaître les limites de Silex 0.42

Le compilateur garde volontairement une surface réduite. Les fonctionnalités
suivantes ne sont pas implémentées :

- fichiers de verrouillage des packages ;
- interopérabilité C++ et interopérabilité C générale et publique.

## Annoter certains littéraux de listes

L'inférence d'un littéral de liste reste syntaxique. Une liste non vide dont le
premier élément est une variable ou une expression sans type immédiatement
visible demande une annotation :

```sx
let values:Pair<int>[] = [first, second]
```

Retirer cette annotation exigera de déplacer la création du type collection du
parseur vers l'analyse typée.

## Distinguer émission et exécution des cibles

Le backend natif entièrement exécuté sur son hôte cible Apple Silicon macOS.
Linux X64, Windows X64 et Windows ARM64 émettent aussi des exécutables natifs ;
leurs résultats sont liés et validés structurellement depuis macOS, mais cette
version attend encore leur validation d'exécution sur ces systèmes.

L'interpréteur reste le comportement de référence lorsque les deux chemins
couvrent la même opération.

## Garder l'implémentation privée

Le langage n'expose aucune ABI stable, disposition native, IR du compilateur,
configuration de linker, allocation ni handle d'exécution. Les opérations C
bas niveau restent réservées aux implémentations de frontière des packages.

Un fournisseur peut employer des bibliothèques système nommées ou des
frameworks Apple sans archive. Une archive fournie doit correspondre à la cible
— Mach-O ARM64, ELF X64, COFF X64 ou COFF ARM64. Les chemins arbitraires de
bibliothèques et fournisseurs chargés à l'exécution restent absents.

Un adaptateur peut appeler une entrée de table déjà résolue avec
`C.call<func(...) T>`, sans pour autant exposer le chargement dynamique ou des
types C aux applications.

[Revenir à la référence](README.md) ·
[Connaître les limites de l'interopérabilité](../Language/Interop/Limits.md)
