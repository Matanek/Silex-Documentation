# Pass a callback function

A function can become a value, be stored in a field, or be passed to another
function. Its type describes its parameter modes and return type.

## Name an existing function

```sx
func any<T>(values:T[], predicate:func(@T) bool) bool {
    for value in values {
        if predicate(value) {
            return true
        }
    }
    return false
}

func positive(value:@int) bool { return value > 0 }

func main() {
    print(any<int>([-1, 2], positive))
}
```

Write `func(T)` for a callback returning `void`, and `func(T) R` for one
returning `R`. Parameters retain ordinary Silex modes: value, read reference
`@`, or mutable reference `&`. The expected type selects an overloaded
function.

## Bind a method to its instance

An instance method can be extracted from its receiver. The resulting value
keeps that receiver lexically bound, so `self` does not appear in the callback
type.

```sx
var parser = Parser(source)
let read_next:func() Token = parser.next_token
let first = read_next()
```

The receiver is evaluated once during extraction. A mutable method requires a
stable mutable receiver, then writes to the same location on every call. The
expected type selects an overload and may omit trailing parameters that have a
default value.

The bound method also keeps its receiver borrowed. A read method prevents its
modification; a mutable method reserves it exclusively. Use an anonymous scope
when the receiver must be reused afterward:

```sx
var counter = Counter(value:0)
{
    let increment:func() = counter.increment
    increment()
}
print(counter.value)
```

## Write an anonymous function

```sx
let found = any<int>([-1, 2], func(value:@int) bool {
    return value > 0
})
```

Omit the return type when the anonymous function returns `void`:

```sx
func visit(value:int, callback:func(int)) { callback(value) }
visit(42, func(value:int) { print(value) })
```

## Capture an outer binding

An anonymous function captures only the outer bindings it uses. A captured
`var` remains shared: modifying it inside the function modifies the surrounding
variable, and copies of the function value refer to the same binding. A
captured `let` remains immutable.

```sx
var count = 0
var increment = func() { count += 1 }
var same_increment = increment

increment()
same_increment()
print(count) // 2
```

A nested anonymous function may capture a binding from any lexical parent
level; intermediate levels carry that context automatically.

Captures and bound methods are lexical borrows. They do not copy the captured
value or extend its lifetime. A function with captures, or a method bound to a
local instance, can be passed to a synchronous call but cannot be returned
outside the scope that owns its borrows.

Function values are language values. They expose neither a machine address nor
a platform calling convention.

[Back to functions](README.md) ·
[Understand borrows](../Ownership/References.md)
