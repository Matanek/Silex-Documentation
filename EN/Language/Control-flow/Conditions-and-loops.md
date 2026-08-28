# Choose a branch and repeat an action

Silex conditions are always `bool` values. They can select a branch with
`if`, control a `while` loop, or filter iterations in a `for` loop.

## Choose a branch

```sx
func main() {
    let value = 0

    if value < 0 {
        print("negative")
    } elif value == 0 {
        print("zero")
    } else {
        print("positive")
    }
}
```

`else if` is another accepted spelling of `elif`. The first true condition
selects its branch; `else` handles the remaining case.

## Repeat while a condition is true

```sx
var remaining = 3

while remaining > 0 {
    print(remaining)
    remaining--
}
```

The condition is evaluated again before every iteration. A condition that is
false from the start never executes the body.

## Stop or skip an iteration

`break` exits the nearest loop. `continue` abandons only the current iteration
and resumes at the next test of that same loop.

```sx
while ready() {
    if finished() {
        break
    }
    if ignored() {
        continue
    }
    work()
}
```

## Iterate over an integer range

```sx
for index in 0...3 {
    print(index)
}

for index in range(3, 0) {
    print(index)
}
```

Both forms exclude the end bound. `0...3` produces `0`, `1`, then `2`;
`range(3, 0)` produces `3`, `2`, then `1`. Equal bounds produce no iteration.

To traverse a collection, see
[the iteration forms](../Collections/Iteration.md).

[Back to execution control](README.md) ·
[Protect shared state](Mutex.md)
