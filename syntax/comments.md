---
title: Comments & lexical structure
---

# Comments & lexical structure

[← Back to index](../index.html)

## Whitespace

Nar is **not** indentation‑sensitive. Whitespace (spaces, tabs, newlines) only separates tokens.
Use indentation freely to make code readable.

## Comments

Nar has two kinds of comments. Both can appear anywhere whitespace can.

### Line comments

A line comment starts with `//` and runs to the end of the line.

```nar
// This is a line comment.
def x: Int = 1  // It can also follow code.
```

### Block comments

A block comment is delimited by `/*` and `*/` and **may be nested**:

```nar
/* outer
   /* inner */
   still in outer
*/
def y: Int = 2
```

Nested block comments make it safe to comment out a region that already contains a comment.

## Source encoding

Source files are UTF‑8 text. String and character literals can contain arbitrary Unicode
codepoints, both directly and via `\uXXXX` escape sequences.

## Reserved keywords

The following identifiers are reserved and cannot be used as user names:

```
module    import    as        exposing
infix     alias     type      def
hidden    native
left      right     non
if        then      else
let       in
select    case      end
```

Anything else (subject to the [identifier rules](./identifiers.html)) is available for your code.

## Token kinds

A Nar program is a sequence of these tokens:

| Kind | Examples |
|---|---|
| Keyword | `module`, `def`, `select`, `case`, `end`, … |
| Identifier | `foo`, `MyType`, `Nar.Base.List`, `mapHelper`, `x_1` |
| Number literal | `0`, `42`, `0xFF`, `0b1010`, `0o755`, `3.14`, `2.5e-3` |
| String literal | `"hello"`, `"line\nbreak"`, `"snow \u2603"` |
| Character literal | `'a'`, `'\n'`, `'\u00e9'` |
| Punctuation | `(` `)` `[` `]` `{` `}` `,` `:` `=` `\|` `_` `.` `\` `->` |
| Infix operator | one or more characters from `! # $ % & * + - / : ; < = > ? ^ \| ~ \`` |

> Note: `\(` (backslash + open paren) starts a [lambda](./functions.html#lambdas).

## Number literals

Integers can be written in decimal or with a base prefix:

```nar
def dec: Int = 255
def hex: Int = 0xFF      // also 0X
def oct: Int = 0o377     // also 0O
def bin: Int = 0b1111_0  // wait — _ is NOT a digit separator in Nar; use plain digits
def bin2: Int = 0b11110  // = 30
```

> Nar does **not** treat `_` as a digit separator. The example above without `_` is the correct
> form.

Floats use a decimal point and/or scientific notation:

```nar
def a: Float = 1.0
def b: Float = 6.022e23
def c: Float = 2.5E-3
```

A negative literal is just `-` followed by a number — `-` is the prefix negation operator on any
expression.

## String and character escapes

Inside `"..."` and `'...'` you can use these escapes:

| Escape | Meaning |
|---|---|
| `\0` | null |
| `\a` | bell |
| `\b` | backspace |
| `\f` | form feed |
| `\n` | newline |
| `\r` | carriage return |
| `\t` | tab |
| `\v` | vertical tab |
| `\"` | double quote |
| `\'` | single quote |
| `\\` | backslash |
| `\uXXXX` | Unicode codepoint (exactly four hex digits) |

A character literal contains exactly one Unicode codepoint:

```nar
def smiley: Char = '😀'   // direct UTF-8 source is fine
def euro: Char   = '\u20AC'
def newline: Char = '\n'
```
