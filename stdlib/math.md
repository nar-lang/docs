---
title: Standard library — Math
---

# `Nar.Base.Math`

[← Back to index](../index.html)

Numeric types and arithmetic. `Int` and `Float` are both declared as `alias native`, so the actual
representation lives on the host (Lua) side.

```nar
alias native Int
alias native Float
```

## Operators

| Operator | Function | Assoc/prec |
|---|---|---|
| `+` | `add` | left 6 |
| `-` | `sub` | left 6 |
| `*` | `mul` | left 7 |
| `/` | `div` | left 7 |

All four are polymorphic over the `number` constraint variable, meaning they work for both `Int`
and `Float` — but **all operands within one expression must be the same numeric type**. Use
`Math.toFloat` (or `round`/`floor`/`ceil`/`trunc`) to convert.

## Functions

```nar
def native add(x: number, y: number): number
def native sub(x: number, y: number): number
def native mul(x: number, y: number): number
def native div(x: number, y: number): number
def native neg(x: number): number

def native toFloat(n: Int): Float
def native round(n: Float): Int
def native floor(n: Float): Int
def native ceil(n: Float): Int
def native trunc(n: Float): Int

def native toPower(pow: number, n: number): number   // pow ** n
def native sqrt(n: Float): Float
def native logBase(base: Float, n: Float): Float
def native abs(x: number): number

def native remainderBy(n: Int, x: Int): Int
def native modBy(n: Int, x: Int): Int

def native isNan(x: number): Bool
def native isInf(x: number): Bool

def pi: Float = 3.141592653589793
def e:  Float = 2.718281828459045
```

`remainderBy` follows the sign of the dividend; `modBy` follows the sign of the divisor.

## Examples

```nar
def hypot(x: Float, y: Float): Float =
  sqrt(x * x + y * y)

def isEven(n: Int): Bool =
  modBy(2, n) == 0

def degreesToRadians(d: Float): Float =
  d * pi / 180.0
```
