---
title: Standard library — String
---

# `Nar.Base.String`

[← Back to index](../index.html)

Immutable Unicode‑capable strings. `String` is declared `alias native String`; all operations live
in this module.

## Operator

| Operator | Function | Assoc/prec |
|---|---|---|
| `<>` | `append` | right 5 |

```nar
def greet(name: String): String =
  "Hello, " <> name <> "!"
```

## Building & inspecting

```nar
def isEmpty(s: String): Bool
def native length(s: String): Int
def native reverse(s: String): String
def repeat(n: Int, chunk: String): String
def replace(before: String, after: String, s: String): String
```

## Concatenation & splitting

```nar
def native append(a: String, b: String): String
def concat(strings: List[String]): String
def native split(sep: String, string: String): List[String]
def native join(sep: String, strings: List[String]): String
def native words(string: String): List[String]
def native lines(string: String): List[String]
```

## Substrings

```nar
def native slice(begin: Int, end: Int, s: String): String   // half-open, supports negatives
def left(n: Int, s: String): String
def right(n: Int, s: String): String
def dropLeft(n: Int, s: String): String
def dropRight(n: Int, s: String): String
```

## Predicates

```nar
def native contains(sub: String, s: String): Bool
def native startsWith(sub: String, s: String): Bool
def native endsWith(sub: String, s: String): Bool
def native indices(sub: String, s: String): List[Int]
```

## Formatting

```nar
def native toUpper(s: String): String
def native toLower(s: String): String
def pad(n: Int, char: Char, s: String): String
def padLeft(n: Int, char: Char, s: String): String
def padRight(n: Int, char: Char, s: String): String
def native trim(s: String): String
def native trimLeft(s: String): String
def native trimRight(s: String): String
```

## Conversions

```nar
def native toInt(s: String): Maybe[Int]
def native fromInt(n: Int): String

def native toFloat(s: String): Maybe[Float]
def native fromFloat(n: Float): String

def toList(s: String): List[Char]
def native fromList(chars: List[Char]): String

def fromChar(c: Char): String
def native cons(c: Char, s: String): String
def native uncons(s: String): Maybe[( Char, String )]
```

## Higher‑order

```nar
def native map(f: (Char): Char, s: String): String
def native filter(f: (Char): Bool, s: String): String
def native foldl(f: (Char, b): b, acc: b, s: String): b
def native foldr(f: (Char, b): b, acc: b, s: String): b
def native any(f: (Char): Bool, s: String): Bool
def native all(f: (Char): Bool, s: String): Bool
```

## Examples

```nar
import Nar.Base.String as S exposing ((<>))

def slug(title: String): String =
  title
    |> S.toLower
    |> S.split(" ")
    |> S.join("-")

def initials(name: String): String =
  name
    |> S.words
    |> List.filterMap(S.uncons)
    |> List.map(\(( c, _ )) -> S.fromChar(c))
    |> S.concat
```
