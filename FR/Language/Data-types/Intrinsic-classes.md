# Comprendre une classe intrinsèque

Une `intrinsic class` publie une API source dont le stockage et le comportement
sont fournis par le compilateur Silex.

```sx
public intrinsic class Resources {
    public func insert<T>(value:T)
    public func has<T>() bool
}
```

Ses méthodes déclarent leur signature sans corps. Le contrat reste ainsi
découvrable sans présenter une implémentation factice ou un échec d'exécution
comme du code source ordinaire.

Les packages applicatifs ne peuvent pas employer `intrinsic` comme mécanisme
général d'implémentation ou d'interopérabilité. Le compilateur reconnaît un
ensemble fermé de classes canoniques et valide leur signature complète. Une
classe intrinsèque inconnue, un corps de méthode ou une signature différente du
contrat du compilateur produit une erreur de compilation.

Une classe intrinsèque ne déclare ni champ, ni constructeur, ni `drop`, ni
héritage, ni conformance de protocole. Ces détails appartiennent à
l'implémentation fournie. Un consommateur construit et appelle une classe
intrinsèque comme toute classe publique ordinaire.

[Revenir aux types de données](README.md) ·
[Observer les métadonnées d'une valeur](Reflection.md)
