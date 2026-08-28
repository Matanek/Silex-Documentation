# Define and call a function

A function names a behavior, declares the values it receives, and specifies
the type it returns.

```sx
func add(left:int, right:int) int {
    return left + right
}

func main() {
    print(add(20, 22))
}
```

The type after the parameters is the return type. Omit it when the function
returns no value:

```sx
func greet(name:str) {
    print("Hello, ", name)
}
```

## Label arguments

A call may follow parameter order or use their names as labels:

```sx
draw(hero, position)
draw(at:position, sprite:hero)
draw(hero, opacity:0.8, at:position)
```

Named arguments may appear in any order. Positional arguments may precede
them, but no positional argument may follow the first named argument. Each
parameter is provided at most once.

After matching labels, Silex evaluates expressions and passes them in parameter
declaration order. Calls made through a function value or callback always
remain positional.

The name of a public parameter is therefore part of the source interface:
renaming it can break named calls without changing its type.

## Return from every path

```sx
func sign(value:int) str {
    if value < 0 {
        return "negative"
    }
    return "positive"
}
```

A function that returns something other than `void` must return a value or
terminate the program on every reachable path.

[Back to functions](README.md) ·
[Provide default values](Defaults-and-overloads.md)
