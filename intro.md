---
title: Introduction
---

# Introduction & Getting Started

[← Back to index](./index.html)

## What Nar looks like

A complete Nar source file consists of a `module` declaration, optional `import`s, and any number
of top‑level definitions. Top‑level constructs are introduced by one of four keywords: `alias`,
`type`, `infix`, `def`.

```nar
module Greetings

import Nar.Base.String exposing (toUpper)

// A type alias gives a more meaningful name to an existing type.
alias Name = String

// A custom (union) type.
type Greeting
  = Formal(Name)
  | Casual(Name)

// Functions are just `def`s with parameters.
def render(g: Greeting): String =
  select g
    case Formal(n) -> "Good day, " <> toUpper(n) <> "."
    case Casual(n) -> "Hey " <> n <> "!"
  end
```

## A whirlwind tour

### Values and types

```nar
def answer: Int       = 42
def pi: Float         = 3.14159
def yes: Bool         = True
def hello: String     = "Hello, world!"
def first: Char       = 'N'
def nothing: ()       = ()
def primes: List[Int] = [2, 3, 5, 7, 11]
def point: ( Int, Int ) = ( 3, 4 )
def origin: { x: Int, y: Int } = { x = 0, y = 0 }
```

### Functions

```nar
def double(x: Int): Int =
  x * 2

def add(x: Int, y: Int): Int =
  x + y

// Functions are first-class values:
def addOne: (Int): Int =
  add(1)            // partial application

// Lambdas:
def triple = \(x: Int) -> x * 3
```

### Pattern matching

```nar
type Shape
  = Circle(radius: Float)
  | Rectangle(width: Float, height: Float)

def area(s: Shape): Float =
  select s
    case Circle(r)         -> 3.14159 * r * r
    case Rectangle(w, h)   -> w * h
  end
```

### Pipes & composition

```nar
import Nar.Base.List exposing (map, filter)

def evenSquares(xs: List[Int]): List[Int] =
  xs
    |> filter(\(n) -> modBy(2, n) == 0)
    |> map(\(n) -> n * n)
```

## How to read this guide

Nar's syntax is small. Reading it linearly takes about an hour:

1. [Comments and lexical structure](./syntax/comments.html)
2. [Literals & primitive values](./syntax/values.html)
3. [Identifiers & naming](./syntax/identifiers.html)
4. [Functions & lambdas](./syntax/functions.html)
5. [Control flow — `if`, `let`, `select`](./syntax/control-flow.html)
6. [Tuples](./syntax/tuples.html)
7. [Records](./syntax/records.html)
8. [Lists](./syntax/lists.html)
9. [Custom types](./syntax/types.html)
10. [Type aliases](./syntax/aliases.html)
11. [Patterns](./syntax/patterns.html)
12. [Type expressions](./syntax/type-expressions.html)
13. [Operators & `infix`](./syntax/operators.html)
14. [Modules & `import`](./syntax/modules.html)
15. [`native` declarations](./syntax/native.html)

Then explore the [standard library reference](./index.html#3-standard-library) at your own pace.

## Toolchain

Nar is implemented by the [Lunar](https://github.com/nar-lang/lunar) toolchain — both compiler and
runtime are written in Lua, so as long as you can run Lua you can run Nar. A Nar package is a
directory containing a `nar.json` manifest plus `.nar` source files and (optionally) `.lua` native
shims:

```json
{
  "name": "Nar.Base",
  "repository": "github.com/nar-lang/Nar.Base",
  "dependencies": []
}
```

See [native declarations](./syntax/native.html) for how Nar talks to Lua.
