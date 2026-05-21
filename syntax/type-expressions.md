---
title: Type expressions
---

# Type expressions

[← Back to index](../index.html)

A **type expression** describes a Nar value. Type expressions appear in function signatures, value
ascriptions, type aliases, custom type declarations, and — occasionally — patterns.

## Forms

### Named types

A name starting with an upper‑case letter refers to a custom type or alias. Type arguments go
between `[ ]`:

```nar
def x: Int                   = 1
def names: List[String]      = []
def lookup: Dict[String, Int] = Dict.empty
```

Names can be qualified by their module: `Nar.Base.List.List[a]` is the same as `List[a]` once the
module is imported.

### Type parameters

A name starting with a lower‑case letter is a **type variable** — a placeholder that can be
unified with any concrete type:

```nar
def identity(x: a): a = x

def map(f: (a): b, xs: List[a]): List[b] = ...
```

Type parameters are implicitly universally quantified at the start of the type. Within one
declaration the same name always refers to the same type.

#### Built‑in constraint variables

Three lower‑case names are reserved for built‑in constraints — they look like type variables but
limit which types are allowed:

| Variable | Means | Examples |
|---|---|---|
| `number` | `Int` or `Float` | arithmetic on `+`, `-`, `*`, `/` |
| `cmp`    | any totally ordered type | `compare`, `<`, `<=`, ordered `Dict` keys |
| `eq`     | any type that supports `==` | `eq`, `neq`, `Set` membership |

```nar
def add(x: number, y: number): number = ...
def member(x: eq, xs: List[eq]): Bool = ...
def compare(l: cmp, r: cmp): Order = ...
```

Within one definition, every occurrence of the same constraint variable must resolve to the same
concrete type.

### Function types

A function type is written `(arg1, arg2, ...): result`:

```nar
def map: ((a): b, List[a]): List[b] = ...
def fold: ((a, b): b, b, List[a]): b = ...
```

A 0‑argument function type would be ambiguous with `()`, so use unit explicitly: `(()): a`.

### Tuple types

`( T1, T2 )`, `( T1, T2, T3 )`, … are tuple types of fixed length:

```nar
def position: ( Int, Int ) = ( 0, 0 )
```

`()` (with no contents) is the unit type.

### Record types

`{ field1: T1, field2: T2, ... }`:

```nar
def alice: { name: String, age: Int } =
  { name = "Alice", age = 30 }
```

Field order in the type is irrelevant — `{ a: Int, b: String }` and `{ b: String, a: Int }` are
the same type.

#### Extensible records

Some standard‑library functions accept any record that **at least** has the listed fields. They
declare their argument type as `{ ext | field: T, ... }`, where `ext` is a row variable:

```nar
def getX(p: { ext | x: Int }): Int = p.x
```

You will mostly see this pattern in advanced/library code; in everyday programs, plain closed
record types are easier to reason about.

## Where type expressions appear

| Position | Example |
|---|---|
| `def` signature | `def add(x: Int, y: Int): Int = ...` |
| Top‑level value ascription | `def pi: Float = 3.14159` |
| Pattern ascription | `let x: Int = 1 in x` |
| Lambda return type | `\(x: Int): Int -> x + 1` |
| `alias` right‑hand side | `alias Pair[a, b] = ( a, b )` |
| `type` constructor payload | `type Tree[a] = Leaf \| Node(a, Tree[a], Tree[a])` |
| `infix` declaration target | implicitly via the referenced function |

## Type inference

Annotations are almost always optional — Nar infers the most general type for a definition. Adding
annotations is encouraged at module boundaries (top‑level `def`s, exposed types) because the
documentation cost is low and the pay‑off in error messages is large.
