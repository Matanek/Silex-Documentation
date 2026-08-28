# Composer des fragments portables et ciblés

Un package nommé peut fournir le même chemin relatif dans sa racine portable,
sa racine de plateforme et sa racine de cible exacte :

```text
Module/Randomizer.sx
Platform/MacOS/Module/Randomizer.sx
Target/macos-arm64/Module/Randomizer.sx
```

Pour la cible active, ces fichiers contribuent au même module logique. Leurs
portées lexicales ne fusionnent toutefois jamais.

## Atteindre un fragment spécialisé

Le code portable nomme explicitement une déclaration spécialisée :

```sx
let handle:Platform.Handle = Platform.open()
let layout:Target.Layout = Target.layout()
```

`Platform` et `Target` sont des qualificateurs contextuels dérivés du package,
du module logique et de la cible. Ils n'exigent aucun `use` et ne deviennent pas
des espaces de noms exportés.

Chaque fichier conserve ses propres `use` et déclarations `local`. Les
déclarations `package` restent partagées dans le package. Une déclaration
spécialisée non publique est accessible seulement par son qualificateur ; une
déclaration publique contribue à l'interface composée du module.

## Préserver les frontières de package

Cette composition ne traverse normalement jamais un package. Deux packages
qui partagent un préfixe de nom ne peuvent fournir le même module exact, sauf
si le parent accorde au module principal exact de l'enfant la permission
`merge: true`.

Cette fusion étroite compose uniquement les déclarations publiques, conserve
le propriétaire de chaque déclaration et refuse toute collision de nom public.
Les fragments de plateforme ou de cible, les modules plus profonds et les
portées `module` ou `package` restent la propriété de leur package d'origine.

[Revenir aux modules](README.md) ·
[Comprendre les frontières d'un package](Packages.md)
