---
title: Standard library — Result
---

# `Nar.Base.Result`

[← Back to index](../index.html)

`Result[error, value]` represents either a successful `Ok` or a failure `Err` carrying an
explanation.

```nar
type Result[error, value]
  = Ok(value)
  | Err(error)
```

## Functions

```nar
def withDefault(default: a, r: Result[x, a]): a

def map(f: (a): b, r: Result[x, a]): Result[x, b]
def map2(...): Result[x, v]
def map3(...): Result[x, v]
def map4(...): Result[x, v]
def map5(...): Result[x, v]

def andThen(callback: (a): Result[x, b], r: Result[x, a]): Result[x, b]
def mapError(f: (x): y, r: Result[x, a]): Result[y, a]

def toMaybe(r: Result[x, a]): Maybe[a]
def fromMaybe(err: x, m: Maybe[a]): Result[x, a]

def isOk(r: Result[x, a]): Bool
```

## Examples

```nar
import Nar.Base.Result as R
import Nar.Base.String as S

type ParseError
  = NotANumber(String)
  | OutOfRange(Int)

def parseAge(input: String): Result[ParseError, Int] =
  input
    |> S.trim
    |> S.toInt
    |> R.fromMaybe(NotANumber(input))
    |> R.andThen(\(n) ->
        if n >= 0 && n <= 150
          then Ok(n)
          else Err(OutOfRange(n)))
```

## When to use

- `Maybe` for "missing" values where the caller doesn't need to know why.
- `Result` when failures should carry **information** — error messages, error codes, structured
  errors.
- For chains of `Result`‑producing steps, use `andThen` to short‑circuit on the first error.
