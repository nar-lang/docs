---
title: Tuples
---

# Tuples

[← Back to index](../index.html)

A **tuple** groups a fixed number of values of possibly different types.

## Literal & type syntax

```nar
def point: ( Int, Int ) = ( 3, 4 )

def labeled: ( String, Int ) = ( "answer", 42 )

def triple: ( Int, Int, Int ) = ( 1, 2, 3 )
```

The whitespace inside `( ... )` is optional; `(1,2)` is the same value as `( 1, 2 )`. By
convention, the standard library spells tuples with surrounding spaces.

There is no 1‑tuple — `(x)` is just parentheses around an expression.

The 0‑tuple is the **unit** value:

```nar
def nothing: () = ()
```

## Constructing & accessing

```nar
import Nar.Base.Tuple

def origin: ( Int, Int ) = Tuple.pair(0, 0)

def x: Int = Tuple.first(( 3, 4 ))     // 3
def y: Int = Tuple.second(( 3, 4 ))    // 4
```

`Tuple.first`, `Tuple.second`, `Tuple.mapFirst`, `Tuple.mapSecond`, `Tuple.mapBoth` and
`Tuple.pair` are defined in [`Nar.Base.Tuple`](https://github.com/nar-lang/Nar.Base). All of them are written using
plain destructuring:

```nar
def first(( x, _ ): ( x, y )) -> x = x
```

## Destructuring

A tuple pattern is just `( p1, p2, ... )` and can appear anywhere a pattern can — function
parameters, `let`, `select`:

```nar
def addPair(( a, b ): ( Int, Int )) -> Int =
  a + b

def swap(p: ( a, b )) -> ( b, a ) =
  let ( x, y ) = p
  in  ( y, x )
```

When the tuple is the scrutinee of a `select`, several positional cases at once become very
natural:

```nar
def merge(a: List[Int], b: List[Int]) -> List[Int] =
  select ( a, b )
    case ( [], ys )         => ys
    case ( xs, [] )         => xs
    case ( x | xr, y | yr ) =>
      if x <= y then x | merge(xr, b) else y | merge(a, yr)
  end
```

## When to use tuples vs. records

Tuples are great for small, ad‑hoc grouping (often two or three components). When fields have
meaningful names, prefer a [record](./records.html) — it keeps call sites readable and lets you use
record update syntax.
