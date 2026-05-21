---
title: Standard library — Tuple
---

# `Nar.Base.Tuple`

[← Back to index](../index.html)

Two‑element tuples are common enough that the standard library provides a small toolkit. See also
the [Tuples syntax page](../syntax/tuples.html).

```nar
def pair(x: x, y: y): ( x, y )

def first(p: ( x, y )): x
def second(p: ( x, y )): y

def mapFirst(f: (x): a, p: ( x, y )): ( a, y )
def mapSecond(f: (y): b, p: ( x, y )): ( x, b )
def mapBoth(f: (x): a, g: (y): b, p: ( x, y )): ( a, b )
```

## Examples

```nar
import Nar.Base.Tuple as T
import Nar.Base.List as L

def withIndex(xs: List[a]): List[( Int, a )] =
  L.indexedMap(T.pair, xs)

def doubleFirst: ( Int, String ): ( Int, String ) =
  T.mapFirst(\(n) -> n * 2)
```

For tuples of size 3 or more, use record types instead — once you go beyond two components, named
fields are usually clearer.
