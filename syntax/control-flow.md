---
title: Control flow
---

# Control flow — `if`, `let`, `select`

[← Back to index](../index.html)

Nar has no statements: all "control flow" is built out of three expression forms — `if`, `let`,
and `select` — plus ordinary function calls.

## `if`

```nar
if condition then expr1 else expr2
```

- `condition` must have type `Bool`.
- The two branches must have the same type (which becomes the type of the whole `if`).
- The `else` branch is **mandatory**.

```nar
def abs(n: Int): Int =
  if n < 0 then -n else n
```

`if` chains are just nested `if … then … else if …`:

```nar
def signum(n: Int): Int =
  if n > 0 then 1
  else if n < 0 then -1
  else 0
```

## `let`

`let` introduces one or more local bindings whose scope is the expression after `in`.

The simplest form binds a pattern to a value:

```nar
def hypot(x: Float, y: Float): Float =
  let
    sq = \(n) -> n * n
  in
    Math.sqrt(sq(x) + sq(y))
```

You can destructure with any [pattern](./patterns.html):

```nar
def dist(p: ( Float, Float )): Float =
  let ( x, y ) = p
  in Math.sqrt(x * x + y * y)
```

You can declare local **functions** with the same syntax as a top‑level `def` (without the
`def` keyword):

```nar
def collatz(n: Int): Int =
  let
    step(k): Int =
      if modBy(2, k) == 0 then k / 2 else 3 * k + 1
  in
    step(n)
```

### Chaining multiple `let`s

You may write several `let` clauses without intermediate `in`s — only the final one is followed by
`in`:

```nar
def stats(xs: List[Int]): ( Int, Int ) =
  let n   = List.length(xs)
  let sum = List.sum(xs)
  in ( n, sum )
```

Bindings see each other in lexical order — later bindings can refer to earlier ones, and a single
binding can be recursive.

## `select`

`select` is Nar's pattern‑match expression:

```nar
select expr
  case pattern1 -> result1
  case pattern2 -> result2
  ...
end
```

- The `case` arms are tried top to bottom; the first matching pattern wins.
- All arms must produce values of the same type.
- A `select` must have at least one `case`, and **must** be terminated by `end`.

```nar
type Shape
  = Circle(Float)
  | Square(Float)
  | Rectangle(Float, Float)

def area(s: Shape): Float =
  select s
    case Circle(r)         -> 3.14159 * r * r
    case Square(side)      -> side * side
    case Rectangle(w, h)   -> w * h
  end
```

You can pattern‑match on tuples to "switch on multiple values at once":

```nar
def merge(a: List[Int], b: List[Int]): List[Int] =
  select ( a, b )
    case ( [], ys )                 -> ys
    case ( xs, [] )                 -> xs
    case ( x | xr, y | yr ) ->
      if x <= y
        then x | merge(xr, b)
        else y | merge(a, yr)
  end
```

The wildcard pattern `_` is the catch‑all "default" case:

```nar
def isVowel(c: Char): Bool =
  select c
    case 'a' -> True
    case 'e' -> True
    case 'i' -> True
    case 'o' -> True
    case 'u' -> True
    case _   -> False
  end
```

See [Patterns](./patterns.html) for every kind of pattern available.

## `Debug.todo` for unfinished branches

When you're sketching code, `Nar.Base.Debug.todo` is a function returning `a` for any `a`; it
crashes if it is ever evaluated. Useful inside an `if` or `select`:

```nar
def parseColor(s: String): Color =
  select s
    case "red"   -> Red
    case "green" -> Green
    case _       -> Debug.todo("more colors please")
  end
```
