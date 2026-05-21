---
title: Type aliases
---

# Type aliases

[← Back to index](../index.html)

A `alias` declaration introduces a **new name** for an existing type. Aliases do not create a
distinct type — they are completely transparent to the type checker.

## Syntax

```nar
alias [hidden] [native] Name[type-parameters] = Type
```

- `Name` must start with an upper‑case letter.
- `[a, b, ...]` are optional type parameters (lower‑case).
- `= Type` provides the right‑hand side. **It is omitted only for `native` aliases**, where the
  underlying representation is provided by the host runtime.

## Examples

### A friendly synonym

```nar
alias Name = String
alias Age  = Int
```

`Name` and `String` are interchangeable everywhere.

### Records

Aliasing a record type is the canonical way to give a record a name:

```nar
alias Point = { x: Int, y: Int }

def origin: Point = { x = 0, y = 0 }

def translate(dx: Int, dy: Int, p: Point): Point =
  { p | x = p.x + dx, y = p.y + dy }
```

### Parameterised aliases

```nar
alias Pair[a, b] = ( a, b )

def first(p: Pair[a, b]): a =
  let ( x, _ ) = p
  in x
```

### Function type aliases

```nar
alias Reducer[a, b] = (a, b): b

def myFold(f: Reducer[Int, Int], xs: List[Int]): Int =
  List.foldl(f, 0, xs)
```

### `native` aliases

A `native` alias declares a type whose representation lives on the host side (Lua). The standard
library uses these for primitive types:

```nar
alias native Int
alias native Float
alias native String
alias native Char
alias native List[a]
```

You write no `=` — the runtime knows what `Int` etc. actually are. See
[`native` declarations](./native.html).

### `hidden` aliases

`alias hidden Foo = ...` makes the alias usable only inside the declaring module. Useful for
internal "newtype‑like" wrappers.

## Alias vs. `type`

| | `alias` | `type` |
|---|---|---|
| Creates a new distinct type | No | Yes |
| Has constructors | No | Yes (one or more `\|`‑separated options) |
| Recommended for | giving names to records, tuples, function types, primitive synonyms | unions, opaque types, ADTs |

If you want a wrapper that the type checker treats as different from its underlying
representation, use a single‑constructor `type`:

```nar
type UserId = UserId(Int)

def myId: UserId = UserId(7)
```

This is sometimes called the "newtype" pattern.
