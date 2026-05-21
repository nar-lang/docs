---
title: Standard library — Char
---

# `Nar.Base.Char`

[← Back to index](../index.html)

A `Char` is a single Unicode codepoint. `Char` is declared `alias native Char`.

```nar
def isUpper(char: Char): Bool
def isLower(char: Char): Bool
def isAlpha(char: Char): Bool
def isAlphaNum(char: Char): Bool
def isDigit(char: Char): Bool
def isOctDigit(char: Char): Bool
def isHexDigit(char: Char): Bool

def native toUpper(char: Char): Char
def native toLower(char: Char): Char

def native toCode(char: Char): Int
def native fromCode(code: Int): Char
```

The classification helpers are limited to ASCII — they check ranges `0x30‑0x39`, `0x41‑0x5A`,
`0x61‑0x7A`, etc. For full Unicode classification you'll need a separate library.

## Examples

```nar
import Nar.Base.Char

def shout(c: Char): Char =
  if Char.isLower(c) then Char.toUpper(c) else c

def hexValue(c: Char): Maybe[Int] =
  if Char.isHexDigit(c) then
    let code = Char.toCode(c)
    in
      if Char.isDigit(c) then
        Just(code - 0x30)
      else
        Just((Bitwise.or(code, 0x20)) - 0x61 + 10)
  else
    Nothing
```
