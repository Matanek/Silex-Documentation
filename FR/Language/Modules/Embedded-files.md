# Intégrer un fichier dans l'exécutable

`embed_text` lit un fichier UTF-8 pendant la compilation et produit un `str`
ordinaire stocké dans l'exécutable.

```sx
func main() {
    let page = embed_text("Web/index.html")
    print(page)
}
```

Le chemin est relatif au fichier `.sx` qui contient l'appel et doit être connu
à la compilation. Un `let` immuable directement lié à un littéral est aussi
accepté. Silex enregistre le fichier comme dépendance du cache : modifier son
contenu reconstruit l'exécutable.

Le fichier source n'est plus nécessaire à l'exécution. `embed_text` refuse un
UTF-8 invalide et limite actuellement chaque fichier à 16 Mio. Le texte obtenu
peut représenter du HTML, un shader, une configuration ou tout autre format,
sans fonction intrinsèque propre au package consommateur.

## Intégrer des octets exacts

```sx
func main() {
    let image:uint8[] = embed_bytes("Web/logo.png")
    print(image.count())
}
```

`embed_bytes` accepte tous les octets, y compris zéro et les séquences UTF-8
invalides, puis renvoie une liste `uint8[]` possédée. Chaque évaluation crée un
stockage indépendant : modifier une liste ne change ni l'image incluse dans
l'exécutable ni le résultat d'une autre évaluation.

Les deux fonctions partagent les règles de chemin relatif, valeur connue à la
compilation, dépendance de cache et limite de 16 Mio.

`embed_text` et `embed_bytes` sont des fonctions réservées du langage. Elles ne
peuvent pas être redéclarées, utilisées comme liaisons ni importées sous alias.

[Revenir aux modules](README.md) ·
[Comprendre les classes intrinsèques](../Data-types/Intrinsic-classes.md)
