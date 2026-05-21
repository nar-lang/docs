---
title: Literals & primitive values
---

# Literals & primitive values

[← Back to index](../index.html)

Nar has a small set of built‑in primitive values. Most of them are defined as `alias native ...` in
[`Nar.Base`](https://github.com/nar-lang/Nar.Base), so the language itself only knows how to write
their literal form.

| Type | Literal example | Module |
|---|---|---|
| `Unit` (`()`) | `()` | `Nar.Base.Basics` |
| `Bool` | `True`, `False` | `Nar.Base.Basics` |
| `Int` | `42`, `0xFF`, `0o17`, `0b101` | `Nar.Base.Math` |
| `Float` | `3.14`, `2.5e-3` | `Nar.Base.Math` |
| `Char` | `'a'`, `'\n'`, `'\u20AC'` | `Nar.Base.Char` |
| `String` | `"hello"` | `Nar.Base.String` |
| `List[a]` | `[]`, `[1, 2, 3]` | `Nar.Base.List` |
| Tuple | `( 1, 2 )`, `( 'a', 1, True )` | `Nar.Base.Tuple` |
| Record | `{ x = 1, y = 2 }` | built‑in |

## Unit

`()` is the only value of the `Unit` type. It is used when a function takes or returns "no
meaningful value":

```nar
def hello: ( () ): String =
  \( () ) -> "hello"
```

`Unit` can also pattern‑match as `()`.

## Booleans

```nar
def yes: Bool = True
def no:  Bool = False
```

Boolean operators are infix functions defined in `Nar.Base.Basics`:

| Operator | Meaning | Function |
|---|---|---|
| `&&` | logical AND | `and` |
| `\|\|` | logical OR | `or` |
| `^^` | logical XOR | `xor` |
| `not(b)` | negation | `not` |

## Numbers

Nar has two numeric types: `Int` and `Float`. Arithmetic operators (`+`, `-`, `*`, `/`) are defined
in `Nar.Base.Math` and use a built‑in `number` type variable so they work for both types — but
inside one expression all operands must agree (`Int + Int → Int`, `Float + Float → Float`). Use
`Math.toFloat` to widen, `round` / `floor` / `ceil` / `trunc` to narrow.

```nar
def total: Int     = 1 + 2 + 3
def avg:   Float   = Math.toFloat(total) / 3.0
def hex:   Int     = 0xCAFE
```

The unary minus is just the regular `-` operator used as a prefix:

```nar
def neg: Int = -42
def alsoNeg: Int = -(1 + 2)
```

## Characters

```nar
def n: Char = 'N'
def é: Char = '\u00E9'
```

`Char` is a single Unicode codepoint. The `Nar.Base.Char` module provides
`toCode` / `fromCode` / `toUpper` / `toLower` / classification helpers.

## Strings

Strings are written between double quotes and support the [escapes listed in the lexical structure
page](./comments.html#string-and-character-escapes).

```nar
def title: String = "Nar — a small functional language"
def multiline: String = "first\nsecond\nthird"
```

There are no triple‑quoted or raw strings; embed `\n`, `\t`, etc. as needed.

The `<>` operator from `Nar.Base.String` concatenates strings:

```nar
def greet(name: String): String =
  "Hello, " <> name <> "!"
```

Because `String` is an opaque alias around the host's string type, all string operations live in
`Nar.Base.String` (`length`, `slice`, `split`, `join`, `toUpper`, `toLower`, `trim`, …).
See the [`Nar.Base`](https://github.com/nar-lang/Nar.Base) repository for details.

## Lists, tuples and records

These have dedicated pages because they appear both as **values** and as **types**:

- [Lists](./lists.html)
- [Tuples](./tuples.html)
- [Records](./records.html)
