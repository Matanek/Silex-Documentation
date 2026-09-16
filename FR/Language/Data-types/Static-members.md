# Définir des membres et des conteneurs statiques

Un membre `static` appartient au type lui-même. Sélectionnez-le toujours avec
le nom complet du type, jamais avec une instance.

```sx
struct Position {
    var x:int
    static let tile_width:int = 32

    static func origin() Position {
        return Position()
    }
}

func main() {
    let origin = Position.origin()
    print(origin.x)
}
```

Une classe accepte les mêmes membres statiques. Ils ne sont ni hérités ni
distribués dynamiquement.

Une [propriété statique](Properties.md#initialiser-paresseusement-un-membre-statique)
peut initialiser son propre stockage à la première lecture, notamment pour
exprimer un singleton sans second champ de cache.

## Créer un conteneur sans instance

Déclarez `static struct` ou `static class` lorsqu'un type sert uniquement à
qualifier des constantes, un état partagé ou des opérations :

```sx
public static struct Constants {
    let canvas_width:int = 960
    let canvas_height:int = 640

    func area() int {
        return Constants.canvas_width * Constants.canvas_height
    }
}
```

Tous les champs et méthodes deviennent implicitement statiques. Le style
canonique omet donc `static` sur chacun d'eux ; le modificateur redondant reste
accepté pour compatibilité source.

Un conteneur statique ne peut pas être construit et ne possède ni constructeur,
`self`, `drop`, classe de base, conformance de protocole, membre `protected`,
extension ni paramètres de type propres au conteneur. Ses méthodes peuvent
toutefois déclarer leurs propres paramètres de type. Les structures et classes
ordinaires imbriquées restent constructibles, sauf si leur propre déclaration
porte aussi `static`.

## Initialiser à la compilation

Silex choisit la stratégie selon la valeur, indépendamment de `let` ou `var`.
Une valeur représentable statiquement est évaluée entièrement à la compilation.
Elle peut utiliser des littéraux intrinsèques, des opérateurs, des conversions
numériques, des champs statiques immuables, des fonctions prouvées évaluables à
la compilation et des constructeurs également prouvés de structures valeurs
dont les champs sont eux-mêmes statiques.

```sx
struct Vec2 {
    var x:float
    var y:float

    init(x:float, y:float) {
        self.x = x
        self.y = y
    }
}

static struct Waypoints {
    let first:Vec2 = Vec2(-200.0, 200.0)
    let second:Vec2 = Vec2(200.0, 200.0)
}
```

La valeur construite est rangée directement dans les données statiques du
programme. Lire `Waypoints.first` en produit une copie et ne rappelle pas son
constructeur à l'exécution.

Une fonction évaluée à la compilation n'utilise que des scalaires intrinsèques,
des variables locales immuables et d'autres appels évaluables à la compilation.
Ce chemin ne peut ni lire un `static var`, ni produire un effet, ni allouer une
ressource d'exécution, ni former un cycle de dépendances.

## Initialiser avant `main`

Une liste dynamique possédée demande un stockage d'exécution. Silex génère
alors un initialiseur exécuté avant `main` ou avant l'entrée isolée d'un test :

```sx
static struct Paths {
    let triangle:int[] = [1, 2, 3]
    var editable:int[] = [0]
}
```

`let` rend le champ statique immuable sans exiger que sa valeur vive dans les
données binaires. `Paths.triangle` peut être lu, indexé et parcouru, mais ni
réaffecté ni redimensionné. `var` permet la réaffectation et les mutations de
liste telles que `Paths.editable.append(4)`.

Les champs statiques d'exécution sont initialisés une seule fois, dans leur
ordre de déclaration. Un initialiseur peut lire un champ d'exécution déclaré
plus tôt ; lire son propre champ ou un champ ultérieur est refusé. Leur stockage
possédé reste vivant pendant tout le programme ou toute la session de test
isolée. Lors du retour normal de `main` ou de l'entrée du test, Silex libère
les valeurs statiques possédées après les valeurs locales, dans l'ordre inverse
de déclaration des champs. Cela comprend les objets conservés dans un cache
statique optionnel. Des champs qui partagent le même objet libèrent chacun leur
référence ; l'objet est détruit lorsque sa dernière référence est libérée.

Les valeurs actuellement admises dans ce chemin d'exécution sont les listes
dynamiques possédées et les structures valeurs qui les contiennent.

[Revenir aux types de données](README.md) ·
[Exécuter un nettoyage déterministe](Cleanup.md)
