---
title: Standard library — Basics
---

# `Nar.Base.Basics`

[← Back to index](../index.html)

Booleans, equality, ordering, and the universal "function helpers" (pipes and composition).

```nar
import Nar.Base.Basics
// or, more commonly, just:
//   import Nar.Base.Basics exposing (...)
// — most of what's here is auto-imported into every module.
```

## Booleans

```nar
type Bool
  = True
  | False

def native not(x: Bool): Bool
def native and(x: Bool, y: Bool): Bool
def native or(x: Bool, y: Bool): Bool
def native xor(x: Bool, y: Bool): Bool
```

| Operator | Function | Assoc/prec |
|---|---|---|
| `&&` | `and` | right 3 |
| `\|\|` | `or` | right 2 |
| `^^` | `xor` | right 2 |

```nar
def isPositive(n: Int): Bool = n > 0 && n < 100
```

## Equality

```nar
def native eq(a: eq, b: eq): Bool
def native neq(a: eq, b: eq): Bool
```

| Operator | Function | Assoc/prec |
|---|---|---|
| `==` | `eq` | non 4 |
| `!=` | `neq` | non 4 |

Equality is **structural** — two values are equal if they have the same shape. Functions cannot be
compared.

## Ordering

```nar
type Order
  = LT | EQ | GT

def native lt(l: cmp, r: cmp): Bool
def native gt(l: cmp, r: cmp): Bool
def native le(l: cmp, r: cmp): Bool
def native ge(l: cmp, r: cmp): Bool

def min(x: cmp, y: cmp): cmp
def max(x: cmp, y: cmp): cmp
def compare(l: cmp, r: cmp): Order
```

| Operator | Function |
|---|---|
| `<`  | `lt` |
| `>`  | `gt` |
| `<=` | `le` |
| `>=` | `ge` |

`cmp` covers `Int`, `Float`, `Char`, `String`, lists, tuples, sets, dicts and arrays of `cmp`.

## Function helpers

```nar
def identity(x: a): a
def always(a: a, _: b): a

def apL(f: (a): b, x: a): b      // backing <|
def apR(x: a, f: (a): b): b      // backing |>

def composeL(g: (b): c, f: (a): b, x: a): c
def composeR(f: (a): b, g: (b): c, x: a): c
```

| Operator | Function | Assoc/prec |
|---|---|---|
| `<\|` | `apL` | right 0 |
| `\|>` | `apR` | left 0 |
| `<~` | `composeL` | left 9 |
| `~>` | `composeR` | right 9 |

```nar
xs |> List.filter(positive) |> List.map(double)

def shout: (String): String =
  String.toUpper ~> String.reverse
```

## `Never`

```nar
type Never
  = hidden JustOneMore(Never)

def never(x: Never): a
```

`Never` is the type of values that cannot exist — there is no way to construct one. `never` is
useful as the result of impossible cases.
