# Adapt a call with default values and overloads

A default value makes trailing arguments optional. An overload lets one name
select several behaviors according to the received types.

## Declare default values

```sx
func greet(message:str = "Hello", repetitions:int = 1) {
    print(message, " x", repetitions)
}

func main() {
    greet()
    greet("Hi")
    greet(repetitions:2)
}
```

Parameters with default values form one continuous suffix of the declaration.
A named call may omit one while providing a later parameter. Each omitted
expression is evaluated at the call site, not when the function is declared.

In a generic callable, a default value is checked against concrete types only
when a call omits it. A specialization may therefore provide an explicit
argument even when the declared default does not match that concrete type.

## Declare overloads

```sx
func describe(value:int) str { return "integer" }
func describe(value:str) str { return "string" }
```

Parameter types distinguish overloads. The return type and parameter names do
not. Two declarations cannot expose the same call prefix through their default
values.

Corresponding parameters in an overload family must have the same names: a
label never selects an overload. The compiler prefers the overload requiring
the least costly implicit conversions.

When an integer can feed both a `float` and a `float64` overload, Silex prefers
`float`, meaning `float32`. A value already typed as `float64` selects the
`float64` overload. This rule applies to positional and named calls alike.

An applicable concrete overload takes priority over generic function
inference.

[Back to functions](README.md) ·
[Adapt a function to several types](Generics.md)
