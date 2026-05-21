---
title: Standard library — Dict
---

# `Nar.Base.Dict`

[← Back to index](../index.html)

`Dict[k, v]` is an immutable, ordered map keyed by any comparable type. Internally it is a
red‑black tree, with a `hidden` constructor — outside the module the type is opaque.

```nar
type Dict[k, v]      // opaque
```

Keys must be `cmp` (numbers, characters, strings, lists/tuples of comparables).

## Construction

```nar
def empty: Dict[k, v]
def singleton(key: cmp, value: v): Dict[k, v]
def fromList(assocs: List[( cmp, v )]): Dict[cmp, v]
```

## Inspection

```nar
def get(key: cmp, dict: Dict[cmp, v]): Maybe[v]
def member(key: cmp, dict: Dict[cmp, v]): Bool
def size(dict: Dict[k, v]): Int
def isEmpty(dict: Dict[k, v]): Bool
```

## Modification

```nar
def insert(key: cmp, value: v, dict: Dict[cmp, v]): Dict[cmp, v]
def remove(key: k, dict: Dict[k, v]): Dict[k, v]
def update(key: k, alter: (Maybe[v]): Maybe[v], dict: Dict[k, v]): Dict[k, v]
```

`update` lets you insert, change or remove a key in a single pass:

```nar
def increment(key: String, d: Dict[String, Int]): Dict[String, Int] =
  Dict.update(
    key,
    \(maybe) ->
      select maybe
        case Just(n) -> Just(n + 1)
        case Nothing -> Just(1)
      end,
    d)
```

## Combine

```nar
def union(t1: Dict[cmp, v], t2: Dict[cmp, v]): Dict[cmp, v]
def intersect(t1: Dict[cmp, v], t2: Dict[cmp, v]): Dict[cmp, v]
def diff(t1: Dict[k, a], t2: Dict[k, b]): Dict[k, a]
def merge(...)                                   // see source
```

## Listing & folding

```nar
def keys(dict: Dict[k, v]): List[k]
def values(dict: Dict[k, v]): List[v]
def toList(dict: Dict[k, v]): List[( k, v )]

def map(f: (k, a): b, dict: Dict[k, a]): Dict[k, b]
def foldl(f: (k, v, b): b, acc: b, dict: Dict[k, v]): b
def foldr(f: (k, v, b): b, acc: b, dict: Dict[k, v]): b
def filter(pred: (k, v): Bool, dict: Dict[k, v]): Dict[k, v]
def partition(pred: (cmp, v): Bool, dict: Dict[cmp, v]): ( Dict[cmp, v], Dict[cmp, v] )
```

## Examples

```nar
import Nar.Base.Dict as D

def wordCount(words: List[String]): Dict[String, Int] =
  List.foldl(
    \(w, d) ->
      D.update(
        w,
        \(m) ->
          select m
            case Just(n) -> Just(n + 1)
            case Nothing -> Just(1)
          end,
        d),
    D.empty,
    words)
```
