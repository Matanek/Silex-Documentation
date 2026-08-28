# Retrouver une forme syntaxique

Cette page sert de repère rapide. Suivez le lien du concept pour comprendre son
comportement et ses contraintes.

| Intention | Forme |
| --- | --- |
| Variable immuable | `let name:type = value` |
| Variable modifiable | `var name:type = value` |
| Commentaire de ligne recommandé | `// commentaire` |
| Commentaire de ligne alternatif | `# commentaire` |
| Commentaire de bloc | `/* commentaire */` |
| Chaîne | `"texte"` |
| Chaîne en bloc | `"` puis des lignes indentées et un `"` seul sur sa ligne |
| Interpolation | `"valeur : $(expression)"` |
| Fonction | `func name(value:type) ReturnType { ... }` |
| Appel nommé | `name(value:expression)` |
| Appel mixte | `name(expression, other:expression)` |
| Structure | `struct Name { ... }` |
| Classe | `class Name { ... }` |
| Contrat fourni par le compilateur | `intrinsic class Name { ... }` |
| Protocole | `protocol Name { ... }` |
| Extension | `extend Name { ... }` |
| Contribution à un catalogue | `contribute GFX.Catalog { public use GFX.Child.Type }` |
| Variante d'enum | `caseName(Type)` dans un `enum` |
| Variante sans contenu | `Enum.caseName` |
| Variante avec contenu | `Enum.caseName(value)` |
| Contenu ignoré dans `match` | `caseName(_)` |
| Branche gardée | `caseName(value) if condition => result` |
| Affectation optionnelle sûre | `optional?.field = value` |
| Extraction optionnelle forcée | `optional!` |
| Remplacement optionnel | `optional ?? fallback` |
| Constructeur | `init(value:type) { ... }` |
| Import d'un package global | `use STD.UUID` |
| Import ancré au package | `use Package.UUID` |
| Import ancré au dossier courant | `use Module.UUID` |
| Chemin qualifié du package | `Package.UUID.Value` |
| Chemin qualifié du dossier | `Module.UUID.Value` |
| Liaison de fonction C | `let name = C.function<func(...) Return>(...)` |
| Appel d'une adresse C | `C.call<func(...) Return>(address, ...)` |
| Alias | `use Existing.Type as LocalName` |
| Déclaration publique | `public ...` |
| Déclaration du package | `package ...` |
| Déclaration du module | `module ...` |
| Déclaration du fichier | `local ...` |
| Optionnel | `Type?` |
| Optionnel imbriqué | `Type??` |
| Tuple nommé | `(width:int, height:int)` |
| Tuple positionnel | `(int, int)` |
| Motif tuple emprunté | `(@Velocity, &Transform)` |
| Déstructuration de tuple | `let (first, second) = value` |
| Tableau fixe | `Type[3]` |
| Liste dynamique | `Type[]` |
| Vue partagée | `@Type[..]` |
| Vue modifiable | `&Type[..]` |
| Paramètre en lecture | `value:@Type` |
| Paramètre modifiable | `value:&Type` |
| Copie détachée | `copy value` |
| Transfert | `move value` |
| Conversion contrôlée | `value as Type` |
| Propagation d'une réussite | `try operation()` |
| Section critique | `mutex { ... }` |
| Cascade de méthode | `value..update()` |
| Méthode d'instance liée | `receiver.method` |
| Réflexion | `reflect(value)` |
| Affectation en cascade | `value..field = replacement` |

`match` est un mot de contrôle en position d'expression, mais reste contextuel
après `func`, `.`, `?.` ou `..`. Une méthode peut donc s'appeler naturellement
`match` : `func match(...)` et `pattern.match(text)`.

`in` reste le mot d'itération, mais devient contextuel comme nom de variante,
dans une branche `match` et après `.` ou `?.` : `Easing.in` est valide.

Les suffixes `?`, `[]` et `[N]` s'appliquent de gauche à droite. `Type?[]` et
`Type[]?` sont donc différents.

## Instructions et portées

```sx
if condition {
} elif other {
} else {
}

while condition {
    break
    continue
}

for value in collection {
}

for index, value in collection.indexed() {
}

for value in start...end {
}

mutex {
    update_shared_state()
}

{
    let temporary = prepare()
    consume(temporary)
}

return value
print(value)
assert(condition)
assert(condition, "message")
panic("message")
```

Un bloc nu est une portée lexicale anonyme, pas une expression. Il s'exécute
une fois, ne porte aucun point-virgule final, masque ses variables après `}` et
les nettoie avant toute sortie normale ou transférée. `break` et `continue`
ciblent toujours la boucle englobante la plus proche.

## Priorité des opérateurs

Du lien le plus fort au plus faible :

```text
as
-  !  try  copy  move
*  /  %
+  -
<<  >>
&
^
<  <=  >  >=
==  !=
&&
||
..method(...)  ..field = value
```

La cascade `..` se lie moins fortement que les opérateurs ordinaires. Un point
simple après un segment de méthode reprend l'accès ordinaire sur son résultat.
La plage `...` reste un token différent de `..`.

Une instruction se termine au retour à la ligne, avant `}` ou avec `;`. Deux
instructions sur une même ligne demandent un point-virgule.

Une liaison `C.function` est le seul `let` accepté au niveau du module dans
Silex 0.42. Consultez [l'interopérabilité](../Language/Interop/README.md) pour
sa signature et sa durée de vie exactes.

[Revenir à la référence](README.md)
