---
title: Standard library — List
---

# `Nar.Base.List`

[← Back to index](../index.html)

Immutable singly‑linked lists, the work‑horse sequence type. See also the [Lists syntax
page](../syntax/lists.html) for literals and patterns.

## Operators

| Operator | Function | Assoc/prec |
|---|---|---|
| `++` | `append` | right 5 |
| `\|` | `cons` | right 5 |

## Construction

```nar
alias native List[a]

def native cons(head: a, tail: List[a]): List[a]
def singleton(value: a): List[a]
def repeat(n: Int, value: a): List[a]
def range(lo: Int, hi: Int): List[Int]
```

## Inspection

```nar
def isEmpty(xs: List[a]): Bool
def length(xs: List[a]): Int
def head(xs: List[a]): Maybe[a]
def tail(xs: List[a]): Maybe[List[a]]
def member(x: eq, xs: List[eq]): Bool
def all(pred: (a): Bool, xs: List[a]): Bool
def any(pred: (a): Bool, xs: List[a]): Bool
def maximum(xs: List[cmp]): Maybe[cmp]
def minimum(xs: List[cmp]): Maybe[cmp]
def sum(xs: List[number]): number
def product(xs: List[number]): number
```

## Transform

```nar
def map(f: (a): b, xs: List[a]): List[b]
def indexedMap(f: (Int, a): b, xs: List[a]): List[b]
def filter(pred: (a): Bool, xs: List[a]): List[a]
def filterMap(f: (a): Maybe[b], xs: List[a]): List[b]
def reverse(xs: List[a]): List[a]
def intersperse(sep: a, xs: List[a]): List[a]
```

## Reduce

```nar
def foldl(f: (a, b): b, acc: b, xs: List[a]): b
def foldr(f: (a, b): b, acc: b, xs: List[a]): b
```

## Combine

```nar
def append(xs: List[a], ys: List[a]): List[a]
def concat(xss: List[List[a]]): List[a]
def concatMap(f: (a): List[b], xs: List[a]): List[b]

def native map2(f: (a, b): r, as: List[a], bs: List[b]): List[r]
def native map3(f: (a, b, c): r, as: List[a], bs: List[b], cs: List[c]): List[r]
def native map4(f: (a, b, c, d): r, as: List[a], bs: List[b], cs: List[c], ds: List[d]): List[r]
def native map5(f: (a, b, c, d, e): r, as: List[a], bs: List[b], cs: List[c], ds: List[d], es: List[e]): List[r]
```

## Sorting

```nar
def sort(xs: List[a]): List[a]
def native sortBy(f: (a): b, xs: List[a]): List[a]
def native sortWith(f: (a, a): Order, xs: List[a]): List[a]
```

## Slicing

```nar
def take(n: Int, xs: List[a]): List[a]
def drop(n: Int, xs: List[a]): List[a]
def partition(pred: (a): Bool, xs: List[a]): ( List[a], List[a] )
def unzip(pairs: List[( a, b )]): ( List[a], List[b] )
```

## Examples

```nar
import Nar.Base.List as L

def evenSquares(xs: List[Int]): List[Int] =
  xs
    |> L.filter(\(n) -> modBy(2, n) == 0)
    |> L.map(\(n) -> n * n)

def words: List[String] =
  L.intersperse(", ", ["red", "green", "blue"])

def histogram(xs: List[String]): List[( String, Int )] =
  xs
    |> L.sort
    |> L.foldl(
        \(s, acc) ->
          select acc
            case ( prev, n ) | rest ->
              if prev == s
                then ( prev, n + 1 ) | rest
                else ( s, 1 ) | acc
            case []                  -> [ ( s, 1 ) ]
          end,
        []
      )
    |> L.reverse
```
