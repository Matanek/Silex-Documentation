# Find a syntax form

Use this page as a quick index. Follow a concept link to understand its behavior
and constraints.

| Intention | Form |
| --- | --- |
| Immutable variable | `let name:type = value` |
| Mutable variable | `var name:type = value` |
| Recommended line comment | `// comment` |
| Alternate line comment | `# comment` |
| Block comment | `/* comment */` |
| String | `"text"` |
| Block string | `"` followed by indented lines and a `"` alone on its line |
| Interpolation | `"value: $(expression)"` |
| Function | `func name(value:type) ReturnType { ... }` |
| Named call | `name(value:expression)` |
| Mixed call | `name(expression, other:expression)` |
| Structure | `struct Name { ... }` |
| Class | `class Name { ... }` |
| Non-clonable class | `nocopy class Name { ... }` |
| Compiler-provided contract | `intrinsic class Name { ... }` |
| Protocol | `protocol Name { ... }` |
| Read-only property | `let name:type { get { ... } }` |
| Initialized property | `var name:type = value { get { ... } }` |
| Property with implicit setter | `var name:type { get { ... } }` |
| Property with explicit setter | `var name:type { get { ... } set(value) { ... } }` |
| Property requirement | `name:type { get }` or `name:type { get set }` |
| Extension | `extend Name { ... }` |
| Catalog contribution | `contribute GFX.Components { public use GFX.Physics.RigidBody2D.RigidBody2D }` |
| Enum variant | `caseName(Type)` inside an `enum` |
| Variant without content | `Enum.caseName` |
| Variant with content | `Enum.caseName(value)` |
| Ignored `match` content | `caseName(_)` |
| Guarded branch | `caseName(value) if condition => result` |
| Safe optional assignment | `optional?.field = value` |
| Forced optional extraction | `optional!` |
| Optional fallback | `optional ?? fallback` |
| Constructor | `init(value:type) { ... }` |
| Global package import | `use STD.UUID` |
| Package-anchored import | `use Package.UUID` |
| Current-directory import | `use Module.UUID` |
| Package-qualified path | `Package.UUID.Value` |
| Directory-qualified path | `Module.UUID.Value` |
| C function binding | `let name = C.function<func(...) Return>(...)` |
| C address call | `C.call<func(...) Return>(address, ...)` |
| Alias | `use Existing.Type as LocalName` |
| Public declaration | `public ...` |
| Package declaration | `package ...` |
| Module declaration | `module ...` |
| File declaration | `local ...` |
| Optional | `Type?` |
| Nested optional | `Type??` |
| Named tuple | `(width:int, height:int)` |
| Positional tuple | `(int, int)` |
| Borrowed tuple pattern | `(@Velocity, &Transform)` |
| Tuple destructuring | `let (first, second) = value` |
| Fixed array | `Type[3]` |
| Dynamic list | `Type[]` |
| Shared view | `@Type[..]` |
| Mutable view | `&Type[..]` |
| Read parameter | `value:@Type` |
| Mutable parameter | `value:&Type` |
| Detached copy | `copy value` |
| Transfer | `move value` |
| Checked conversion | `value as Type` |
| Success propagation | `try operation()` |
| Critical section | `mutex { ... }` |
| Method cascade | `value..update()` |
| Bound instance method | `receiver.method` |
| Reflection | `reflect(value)` |
| Cascade assignment | `value..field = replacement` |

`match` is a control word in expression position, but remains contextual after
`func`, `.`, `?.`, or `..`. A method may therefore naturally be named `match`:
`func match(...)` and `pattern.match(text)`.

Primitive type spellings are likewise contextual as method names. A type API
can declare `static func float32()` and be called with `DType.float32()`
without making `float32` available as an ordinary identifier.

`in` remains the iteration word, but becomes contextual as a variant name, in
a `match` branch, and after `.` or `?.`: `Easing.in` is valid.

The `?`, `[]`, and `[N]` suffixes apply from left to right. `Type?[]` and
`Type[]?` are therefore different types.

## Statements and scopes

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

A bare block is an anonymous lexical scope, not an expression. It runs once,
has no trailing semicolon, hides its variables after `}`, and cleans them up
before any normal or transferred exit. `break` and `continue` always target the
nearest enclosing loop.

## Operator precedence

From strongest to weakest binding:

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

The `..` cascade binds less strongly than ordinary operators. A single dot
after a method segment resumes ordinary access on that method's result. The
`...` range remains a different token from `..`.

A statement ends at a line break, before `}`, or with `;`. Two statements on
the same line require a semicolon.

## Operator declarations

```text
[visibility] func operator + (left:T, right:U) R { ... }
[visibility] func operator - (value:T) R { ... }
```

The declarable symbols are `+`, `-`, `*`, and `/`. A declaration appears at
module level, uses one value parameter for negation or two for a binary
operation, and returns a non-void owned value. Defaults, references, and generic
type parameters are not accepted. At least one nominal operand type belongs to
the declaring package.

A `C.function` binding is the only module-level `let` accepted in Silex 0.44.
See [interoperability](../Language/Interop/README.md) for its exact signature
and lifetime.

[Back to the reference](README.md)
