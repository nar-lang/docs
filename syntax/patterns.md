---
title: Patterns
---

# Patterns

[← Back to index](../index.html)

A **pattern** describes the shape of a value and binds names to its parts. Patterns appear in
function parameters, `let` bindings, and `select` / `case` arms.

## Pattern forms

### Wildcard

`_` matches any value and binds nothing.

```nar
def first(p: ( a, b )): a =
  let ( x, _ ) = p
  in x
```

### Variable

A lower‑case identifier matches any value and binds it to that name.

```nar
def double(x: Int): Int = x * 2
```

### Literal

Number, character, string and unit literals match their exact value.

```nar
def isOne(n: Int): Bool =
  select n
    case 1 -> True
    case _ -> False
  end
```

`()` is the unit pattern.

### Tuple

`( p1, p2, ... )` matches a tuple element‑wise. The arity must match exactly.

```nar
def swap(( a, b ): ( x, y )): ( y, x ) =
  ( b, a )
```

### Record

`{ field1, field2, ... }` matches a record and binds each named field to a local variable of the
same name.

```nar
def fullName({ first, last }: { first: String, last: String }): String =
  first <> " " <> last
```

You only need to mention the fields you actually use. Field names listed in the pattern must exist
on the record being matched.

### List

`[]` matches the empty list. `[p1, p2, p3]` matches a list of exactly that length, element‑wise.

```nar
def sum2(xs: List[Int]): Int =
  select xs
    case []      -> 0
    case [a, b]  -> a + b
    case _       -> -1
  end
```

### Cons

`head | tail` matches a non‑empty list. `head` matches the first element, `tail` matches the rest:

```nar
def head(xs: List[a]): Maybe[a] =
  select xs
    case x | _ -> Just(x)
    case []    -> Nothing
  end
```

`|` is right‑associative, so `x | y | z | rest` matches a list whose first three elements are `x`,
`y`, `z` and whose tail is `rest`:

```nar
def take3(xs: List[a]): List[a] =
  select xs
    case x | y | z | _ -> [x, y, z]
    case _             -> xs
  end
```

### Constructor (option)

A name starting with an upper‑case letter is interpreted as a [type
constructor](./types.html). The pattern matches values built with that constructor; sub‑patterns
in parentheses match the payloads positionally:

```nar
def describe(m: Maybe[Int]): String =
  select m
    case Just(n) -> "got " <> String.fromInt(n)
    case Nothing -> "nothing"
  end
```

A no‑payload constructor pattern is just the constructor name with no parens (e.g. `Nothing`,
`True`, `LT`).

### `as` alias

Append `as name` to a pattern to bind the whole matched value to `name` while still destructuring
its parts:

```nar
def trim(m: Maybe[String]): Maybe[String] =
  select m
    case Just(s) as orig ->
      if String.isEmpty(s) then Nothing else orig
    case Nothing -> Nothing
  end
```

### Type ascription

Append `: Type` to a pattern to force a particular type. This is occasionally useful inside
`let` bindings and in lambda parameters:

```nar
let
  ages: List[Int] = []
in
  ages
```

```nar
\(x: Int) -> x + 1
```

## Patterns in function signatures

The parameter list of a `def` or lambda is just a tuple of patterns:

```nar
def first(( x, _ ): ( a, b )): a = x
def addCoords({ x, y }: { x: Int, y: Int }): Int = x + y
```

You can mix annotated and unannotated parameters; only the annotated ones constrain inference.

## Exhaustiveness

`select` arms are tried top to bottom; the first match wins. A `select` should cover every shape
the input might have — the compiler warns about non‑exhaustive matches. Use `_` as the final
case to express "anything else".
