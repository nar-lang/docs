---
title: Standard library — Set
---

# `Nar.Base.Set`

[← Back to index](../index.html)

`Set[a]` is an unordered collection of unique values. It is implemented as a `Dict[a, ()]` and so
requires its element type to be `cmp` (comparable).

```nar
type Set[t]      // opaque
```

## Construction

```nar
def empty: Set[a]
def singleton(value: cmp): Set[cmp]
def fromList(xs: List[cmp]): Set[cmp]
```

## Inspection

```nar
def isEmpty(s: Set[a]): Bool
def size(s: Set[a]): Int
def member(x: cmp, s: Set[cmp]): Bool
```

## Modification

```nar
def insert(x: cmp, s: Set[cmp]): Set[cmp]
def remove(x: cmp, s: Set[cmp]): Set[cmp]
```

## Set algebra

```nar
def union(a: Set[cmp], b: Set[cmp]): Set[cmp]
def intersect(a: Set[cmp], b: Set[cmp]): Set[cmp]
def diff(a: Set[cmp], b: Set[cmp]): Set[cmp]
```

## Listing & higher‑order

```nar
def toList(s: Set[a]): List[a]

def map(f: (cmp): cmp2, s: Set[cmp]): Set[cmp2]
def foldl(f: (a, b): b, acc: b, s: Set[a]): b
def foldr(f: (a, b): b, acc: b, s: Set[a]): b
def filter(pred: (cmp): Bool, s: Set[cmp]): Set[cmp]
def partition(pred: (cmp): Bool, s: Set[cmp]): ( Set[cmp], Set[cmp] )
```

## Examples

```nar
import Nar.Base.Set as S

def unique(xs: List[Int]): List[Int] =
  xs |> S.fromList |> S.toList
```
