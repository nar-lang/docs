---
title: Standard library — Bitwise
---

# `Nar.Base.Bitwise`

[← Back to index](../index.html)

Bit‑level operations on `Int` values.

## Operators

| Operator | Function | Assoc/prec |
|---|---|---|
| `&`   | `and`         | left 7 |
| `^`   | `xor`         | left 6 |
| `%`   | `or`          | left 6 |
| `<<`  | `shiftLeft`   | left 5 |
| `>>`  | `shiftRight`  | left 5 |
| `>>>` | `shiftRightZf` (zero‑fill) | left 5 |

> Inside `Bitwise` the operator `%` aliases the bitwise OR. Don't confuse it with arithmetic
> remainder — for that use `Math.modBy` or `Math.remainderBy`.

## Functions

```nar
def native and(x: Int, y: Int): Int
def native or(x: Int, y: Int): Int
def native xor(x: Int, y: Int): Int
def native complement(x: Int): Int

def native shiftLeftBy(x: Int, y: Int): Int
def native shiftRightBy(x: Int, y: Int): Int
def native shiftRightZfBy(x: Int, y: Int): Int
```

Note the argument order of the `*By` variants — the **shift amount** comes first, the **value**
second. The infix forms (`<<`, `>>`, `>>>`) reverse this so you can write `value << amount`
naturally.

## Examples

```nar
import Nar.Base.Bitwise exposing ((&), (<<), (>>>))

def isPowerOfTwo(n: Int): Bool =
  n > 0 && (n & (n - 1)) == 0

def packRgb(r: Int, g: Int, b: Int): Int =
  (r << 16) % (g << 8) % b
```
