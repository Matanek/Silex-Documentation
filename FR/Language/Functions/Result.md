# Retourner une erreur récupérable

`Result<T,E>` représente soit une réussite `success(T)`, soit un échec
`failure(E)`. Contrairement à `panic`, l'appelant peut examiner ou propager cet
échec.

```sx
enum ParseError {
    invalid(str)
}

func parse(text:str) Result<int,ParseError> {
    if text.count() == 0 {
        return Result<int,ParseError>.failure(ParseError.invalid("vide"))
    }
    return Result<int,ParseError>.success(42)
}

func main() {
    let message = match parse("42") {
        success(value) => "valeur : $(value)"
        failure(error) => "invalide"
    }
    print(message)
}
```

`match` traite ici les deux variantes sans laisser d'échec implicite.

## Propager avec `try`

```sx
func load(text:str) Result<int,ParseError> {
    let value = try parse(text)
    return Result<int,ParseError>.success(value)
}
```

En cas de réussite, `try` produit la valeur `T`. En cas d'échec, il retourne
immédiatement le même type d'erreur depuis la fonction englobante. Il s'agit de
contrôle de flux ordinaire, pas d'une exception.

Pour `Result<void,E>`, construisez la réussite avec `success()` et écrivez
`try operation()` comme une instruction.

## Traiter l'échec localement

Ajoutez un bloc `else` lorsque l'échec doit quitter le flux courant autrement.
Ne liez `error` que si la branche utilise la valeur d'origine :

```sx
func load(text:str) Result<int,AppError> {
    let value = try parse(text) else error {
        return Result<int,AppError>.failure(AppError.input(error))
    }
    return Result<int,AppError>.success(value)
}
```

Écrivez `else { ... }` pour ignorer volontairement l'erreur. Chaque chemin du
bloc doit sortir avec `return`, `break`, `continue` ou une autre terminaison
garantie ; ce bloc ne fournit jamais de valeur de remplacement.

À la frontière d'une commande, une forme courte remplace n'importe quel type
d'erreur par un échec `str` et le retourne immédiatement :

```sx
func load_for_cli(text:str) Result<int,str> {
    let value = try parse(text) else error "analyse impossible : $(text)"
    return Result<int,str>.success(value)
}
```

Le message est évalué une fois sur l'échec et jamais sur la réussite. L'erreur
d'origine n'est volontairement pas disponible dans cette forme.

## Transformer le type d'erreur

```sx
func convert(error:ParseError) AppError {
    return AppError.input(error)
}

let config = map_error(parse(text), convert)
```

`map_error` appelle la transformation nommée exactement une fois sur l'échec et
jamais sur la réussite.

[Revenir aux fonctions](README.md) ·
[Choisir avec un enum et `match`](../Data-types/Enums.md)
