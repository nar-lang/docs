---
title: Lists
---

# Lists

[← Back to index](../index.html)

`List[a]` is the workhorse sequence type. It is a singly‑linked, immutable list — adding or
removing the **head** is cheap, indexing in the middle is not.

## Literals & cons

```nar
def empty:   List[Int] = []
def primes:  List[Int] = [2, 3, 5, 7, 11]
def words:   List[String] = ["hello", "world"]
```

The `|` operator (right‑associative, precedence 5) prepends an element to a list:

```nar
import Nar.Base.List exposing (cons)

def xs: List[Int] = 0 | [1, 2, 3]   // [0, 1, 2, 3]
def ys: List[Int] = cons(0, [1, 2, 3])
```

`cons(head, tail)` and `head | tail` are interchangeable.

`++` (right‑associative, precedence 5) concatenates two lists:

```nar
def all: List[Int] = [1, 2] ++ [3, 4]   // [1, 2, 3, 4]
```

## Pattern matching on lists

The two basic list patterns are `[]` (empty list) and `head | tail` (non‑empty list, head bound to
`head`, tail to `tail`):

```nar
def length(xs: List[a]) -> Int =
  select xs
    case []     => 0
    case _ | xs => 1 + length(xs)
  end
```

You can also write a fixed‑size list pattern, with each element as its own pattern:

```nar
def sumPair(xs: List[Int]) -> Int =
  select xs
    case [a, b] => a + b
    case _      => 0
  end
```

Combine `|` with multiple positional patterns to peek at the first few elements while keeping the
rest:

```nar
def take3(xs: List[a]) -> List[a] =
  select xs
    case x | y | z | _ => [x, y, z]
    case _             => xs
  end
```

## Standard list functions

The [`Nar.Base.List`](https://github.com/nar-lang/Nar.Base) module provides:

- Construction: `singleton`, `repeat`, `range`, `cons`
- Inspection: `isEmpty`, `length`, `head`, `tail`, `member`
- Transform: `map`, `indexedMap`, `filter`, `filterMap`, `concatMap`, `reverse`,
  `intersperse`
- Combine: `append`, `concat`, `map2`, `map3`, `map4`, `map5`, `unzip`
- Reduce: `foldl`, `foldr`, `sum`, `product`, `minimum`, `maximum`, `all`, `any`
- Sorting: `sort`, `sortBy`, `sortWith`
- Slicing: `take`, `drop`, `partition`

```nar
import Nar.Base.List

def evenSquares(xs: List[Int]) -> List[Int] =
  xs
    |> List.filter(\(n) => modBy(2, n) == 0)
    |> List.map(\(n) => n * n)
```

## Lists vs. arrays

If you need fast random access, use `Nar.Base.Array` instead — it is a chunked
immutable vector. Lists are best when you process from the front and recurse on the tail.
