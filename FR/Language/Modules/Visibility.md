# Exposer ou masquer une déclaration

Au niveau d'un module, une déclaration sans modificateur possède la visibilité
`module`. Tous les fichiers possédés par le même module principal peuvent
l'utiliser, y compris ses modules d'implémentation et ses fragments portable,
`Platform` et `Target` sélectionnés.

```sx
func helper() {}

public func start() {
    helper()
}

func main() {
    start()
}
```

## Choisir une portée géographique

- `public` expose la déclaration aux consommateurs du package.
- `package` l'expose aux modules du package et aux packages enfants autorisés
  avec `friend: true`.
- `module` l'expose au module logique principal ; c'est la valeur par défaut.
- `local` la limite au fichier source exact.

```sx
public struct Position { var x:int }
package struct DecodeState {}
local struct ParserState {}
```

Le modificateur `module` explicite reste accepté lorsqu'une frontière demande
à être soulignée, mais son omission est le style courant.

Deux packages partageant un préfixe ne partagent pas `package` sans permission
`friend`. Une déclaration `local` ne franchit jamais son fichier. Les fichiers
possédés par un module principal partagent `module` sans fusionner leurs
portées lexicales ni leurs imports. Sans module principal, deux chemins frères
restent deux frontières distinctes.

`private` et `protected` sont relatifs à un type et sont donc refusés au niveau
du module. `private` appartient à la famille du type déclarant ; `protected`
est réservé aux membres de classe et à leurs descendants.

## Faire hériter les membres de leur type

Un champ, constructeur, méthode, membre statique ou type imbriqué sans
modificateur hérite de la visibilité de son type conteneur.

```sx
public class Session {
    private let token:str

    init(token:str) {
        self.token = token
    }

    func text() str {
        return self.token
    }

    package func debug() {}
}
```

`Session.init` et `Session.text` sont publics par héritage. La visibilité du
type plafonne toujours celle de ses membres : un modificateur explicite peut la
conserver ou la réduire, jamais l'agrandir. Écrire `public func` dans une classe
visible seulement par son module est une erreur, pas une visibilité
silencieusement plafonnée.

Une redéfinition ou une implémentation de protocole conserve la visibilité de
son type concret. Un appel à travers un type de base ou un protocole déjà
accessible conserve le contrat visible ; l'implémentation n'a pas à le publier
à nouveau.

`use` introduit un nom dans son fichier et `public use` le réexporte. Les autres
modificateurs de visibilité ne s'appliquent pas à `use`.

[Revenir aux modules](README.md) ·
[Réexporter une déclaration](Reexports.md)
