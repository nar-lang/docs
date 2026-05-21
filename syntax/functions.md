---
title: Functions & lambdas
---

# Functions & lambdas

[← Back to index](../index.html)

## Top‑level functions

A function is introduced by `def`, followed by a name, an optional **signature** (parameter list
with optional types and an optional return type), `=`, and a body expression.

```nar
def double(x: Int): Int =
  x * 2

def add(x: Int, y: Int): Int =
  x + y
```

The signature is just a parenthesised list of patterns, optionally followed by `: ReturnType`. Any
of the parts can be omitted:

```nar
// fully annotated
def addF(x: Int, y: Int): Int = x + y

// only the return type
def addR(x, y): Int = x + y

// only the parameter types
def addP(x: Int, y: Int) = x + y

// no annotations — the compiler will infer
def addN(x, y) = x + y
```

When **no parameter list** is given, `def` introduces a value rather than a function:

```nar
def pi: Float = 3.141592653589793
```

You can also use a type ascription for a value, then `=` and the expression — exactly as you would
write a parameterless `def`.

## Bodies are expressions

A function body is a single expression. To do "more than one thing", combine expressions with
`let` (see [control flow](./control-flow.html#let)) or `select`.

## Parameters are patterns

Each parameter slot is actually a [pattern](./patterns.html), so you can destructure right in the
signature:

```nar
import Nar.Base.Tuple

def first(( x, _ ): ( x, y )): x =
  x

def fullName({ first, last }: { first: String, last: String }): String =
  first <> " " <> last
```

`(...)` around a single item in a signature still counts as a one‑parameter pattern; if you want a
tuple you have to write the tuple form `( a, b )`.

## Calling a function

Function call uses `f(arg1, arg2, ...)`:

```nar
def sum: Int = add(2, 3)        // 5
def name: String = fullName({ first = "Ada", last = "Lovelace" })
```

There is no juxtaposition application: you must use parentheses.

## Partial application

Calling a function with **fewer** arguments than it declares returns a new function that takes the
rest:

```nar
def add(x: Int, y: Int): Int = x + y

def addOne: (Int): Int = add(1)

def main: Int = addOne(41)   // 42
```

This is heavily used together with the pipe operators (`|>`, `<|`) and `List.map` / `List.filter`:

```nar
def doubled: List[Int] =
  [1, 2, 3] |> List.map(\(n) -> n * 2)
```

> Pattern‑matching a partial application against multiple "argument groups" is **not** allowed —
> a `def` has exactly one parameter list.

## Lambdas

A lambda (anonymous function) starts with `\(` and a parameter list, then `->`, then the body
expression:

```nar
def double = \(x: Int) -> x * 2

def addOne = \(x: Int): Int -> x + 1

def constUnit = \( () ) -> "no input"
```

The parameter list follows exactly the same rules as a `def`'s signature: each slot is a pattern,
optional return type after `:`. Lambdas are first‑class values.

```nar
import Nar.Base.List

def odd(xs: List[Int]): List[Int] =
  List.filter(\(n) -> modBy(2, n) == 1, xs)
```

## Local functions and values: `let`

Local bindings use `let` (see [control flow](./control-flow.html#let)). Inside `let` you can write
either a value binding or a full function definition:

```nar
def normalise(s: String): String =
  let
    trimmed = String.trim(s)
    upper(x): String = String.toUpper(x)
  in
    upper(trimmed)
```

Multiple `let` bindings can be **chained** without `in` — only the final binding ends with `in`:

```nar
def example: Int =
  let x = 1
  let y = 2
  let z = x + y
  in z * 10
```

Local functions can be (mutually) recursive.

## `hidden` functions

Prefix a `def` with `hidden` to hide it from other modules — it remains usable inside its own
module but is not exposed via `exposing` or via qualified name:

```nar
def fact(n: Int): Int = factHelp(1, n)

def hidden factHelp(acc: Int, n: Int): Int =
  if n <= 1 then acc else factHelp(acc * n, n - 1)
```

`hidden` is the Nar equivalent of "private". See [modules](./modules.html#hidden-and-exposing).

## `native` functions

A `def native` declaration says "this function is provided by the host runtime (Lua), look it up by
name". You write the signature but no body:

```nar
def native length(s: String): Int

def native cons(head: a, tail: List[a]): List[a]
```

See [native declarations](./native.html).

## Recursion

Functions can call themselves directly — there is no special `rec` keyword:

```nar
def fact(n: Int): Int =
  if n <= 1 then 1 else n * fact(n - 1)
```

The compiler performs **tail‑call optimisation** on direct tail calls, so writing accumulator‑style
recursive helpers is the normal way to express loops in Nar:

```nar
def sum(xs: List[Int]): Int = sumHelp(0, xs)

def hidden sumHelp(acc: Int, xs: List[Int]): Int =
  select xs
    case []     -> acc
    case x | xs -> sumHelp(acc + x, xs)
  end
```
