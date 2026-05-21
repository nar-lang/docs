---
title: Standard library — Array
---

# `Nar.Base.Array`

[← Back to index](../index.html)

`Array[a]` is an immutable indexed sequence, implemented as a 32‑way Relaxed‑Radix‑Balanced
tree. It gives you near‑O(1) indexing and
update, with `O(log32 n)` worst case — much faster than a `List` for random access.

```nar
type Array[a]      // opaque
```

## Construction

```nar
def empty: Array[a]
def initialize(len: Int, fn: (Int): a): Array[a]
def repeat(n: Int, value: a): Array[a]
def fromList(xs: List[a]): Array[a]
```

## Inspection

```nar
def isEmpty(a: Array[a]): Bool
def length(a: Array[a]): Int
def get(index: Int, a: Array[a]): Maybe[a]
```

## Modification

```nar
def set(index: Int, value: a, arr: Array[a]): Array[a]
def push(value: a, arr: Array[a]): Array[a]
```

## Conversions

```nar
def toList(a: Array[a]): List[a]
def toIndexedList(a: Array[a]): List[( Int, a )]
```

## Higher‑order

```nar
def map(f: (a): b, arr: Array[a]): Array[b]
def indexedMap(f: (Int, a): b, arr: Array[a]): Array[b]
def filter(pred: (a): Bool, arr: Array[a]): Array[a]
def foldl(f: (a, b): b, acc: b, arr: Array[a]): b
def foldr(f: (a, b): b, acc: b, arr: Array[a]): b
```

## Combine & slice

```nar
def append(a: Array[a], b: Array[a]): Array[a]
def slice(from: Int, to: Int, arr: Array[a]): Array[a]
```

## Examples

```nar
import Nar.Base.Array as A

def squares(n: Int): Array[Int] =
  A.initialize(n, \(i) -> i * i)

def lookup(i: Int, xs: Array[Int]): Int =
  A.get(i, xs) |> Maybe.withDefault(0)
```
