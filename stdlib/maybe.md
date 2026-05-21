---
title: Standard library — Maybe
---

# `Nar.Base.Maybe`

[← Back to index](../index.html)

`Maybe[a]` is for values that may be missing.

```nar
type Maybe[a]
  = Just(a)
  | Nothing
```

## Functions

```nar
def withDefault(default: a, m: Maybe[a]): a

def map(f: (a): b, m: Maybe[a]): Maybe[b]
def map2(f: (a, b): v, ma: Maybe[a], mb: Maybe[b]): Maybe[v]
def map3(...): Maybe[v]
def map4(...): Maybe[v]
def map5(...): Maybe[v]

def andThen(callback: (a): Maybe[b], m: Maybe[a]): Maybe[b]

def isJust(m: Maybe[a]): Bool
def destruct(default: b, f: (a): b, m: Maybe[a]): b
```

`destruct` is sometimes called "fold" or "unwrap with default" — it applies `f` to the value
inside `Just`, or returns `default` for `Nothing`.

## Examples

```nar
import Nar.Base.Maybe as M
import Nar.Base.String as S

def parseAge(input: String): Maybe[Int] =
  input
    |> S.trim
    |> S.toInt
    |> M.andThen(\(n) -> if n >= 0 then Just(n) else Nothing)

def displayAge(input: String): String =
  parseAge(input)
    |> M.map(\(n) -> S.fromInt(n) <> " years old")
    |> M.withDefault("(invalid)")
```

## When to use

- Use `Maybe` when "absence" is a normal outcome — looking up a key, parsing a number, taking the
  head of a possibly empty list.
- Use [`Result`](./result.html) when you want to carry an **error explanation** alongside the
  failure case.
