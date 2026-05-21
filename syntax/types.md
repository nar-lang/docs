---
title: Custom (union) types
---

# Custom (union) types

[← Back to index](../index.html)

Custom types — also called **union types** or **algebraic data types** — are how you describe data
that has several distinct shapes. They are the most important modelling tool in Nar.

## Declaration syntax

```nar
type Name[type-parameters]
  = Option1
  | Option2(Type1)
  | Option3(name1: Type1, name2: Type2)
  | hidden Option4(...)
```

- The type name and every option name must start with an upper‑case letter.
- `[a, b, ...]` lists optional **type parameters**, all lower‑case.
- Each `|`‑separated alternative is a **constructor** (also called an **option** in Nar's parser).
- A constructor can take 0 or more **payload** values; each can optionally have a label.
- An option prefixed with `hidden` can only be constructed/matched inside the same module.

The whole `type` declaration may itself be `hidden`:

```nar
type hidden InternalState
  = Idle
  | Running(Int)
```

## Examples

### Enumerations

```nar
type Color
  = Red
  | Green
  | Blue
```

`Red`, `Green`, `Blue` are values of type `Color`.

### Tagged variants

```nar
type Maybe[a]
  = Just(a)
  | Nothing

type Result[error, value]
  = Ok(value)
  | Err(error)
```

The constructors take payload values:

```nar
def safeDivide(x: Int, y: Int): Maybe[Int] =
  if y == 0 then Nothing else Just(x / y)
```

### Constructors with labelled fields

You can give names to a constructor's payloads. The names are mostly documentation, but they show
up in error messages and make the parser accept the value type after `:`:

```nar
type Shape
  = Circle(radius: Float)
  | Rectangle(width: Float, height: Float)
```

Labels do not change how you construct or destructure — payloads are still positional:

```nar
def square(s: Float): Shape = Rectangle(s, s)

def area(sh: Shape): Float =
  select sh
    case Circle(r)       -> 3.14159 * r * r
    case Rectangle(w, h) -> w * h
  end
```

### Recursive types

A type can mention itself in any of its constructors:

```nar
type Tree[a]
  = Leaf
  | Node(a, Tree[a], Tree[a])
```

```nar
def depth(t: Tree[a]): Int =
  select t
    case Leaf            -> 0
    case Node(_, l, r)   -> 1 + max(depth(l), depth(r))
  end
```

## Constructors as functions

Each constructor is a value: an option with no payload is a constant of the type, and an option
with payload is a function `(p1, p2, ...) -> Type`. So you can pass them to higher‑order
functions:

```nar
import Nar.Base.List

def justAll(xs: List[Int]): List[Maybe[Int]] =
  List.map(Just, xs)
```

## Pattern matching

To inspect a custom type, use [`select` / `case`](./control-flow.html#select). The pattern for
constructor `Foo(a, b)` is written `Foo(p1, p2)`; for a 0‑payload constructor it is just `Foo`:

```nar
def isJust(m: Maybe[a]): Bool =
  select m
    case Just(_) -> True
    case Nothing -> False
  end
```

You can also bind the whole value with `as`:

```nar
def describe(m: Maybe[Int]): String =
  select m
    case Just(n) as v -> "got " <> Debug.toString(v)
    case Nothing      -> "nothing"
  end
```

## `hidden` constructors

Marking individual constructors `hidden` lets a module expose the **type** but not its data
representation:

```nar
type hidden NColor
  = Red
  | Black

type Dict[k, v]
  = hidden RBNode(color: NColor, key: k, value: v, left: Dict[k, v], right: Dict[k, v])
  | hidden RBEmpty
```

Outside the module, `Dict[k, v]` is an opaque type — users obtain values only through the module's
public functions (`Dict.empty`, `Dict.insert`, …).

This is exactly how `Nar.Base.Dict` and `Nar.Base.Set` are implemented.

## Built‑in unions

Many "primitives" are actually just unions defined in `Nar.Base`:

- `Bool` — `True | False`
- `Order` — `LT | EQ | GT`
- `Maybe[a]` — `Just(a) | Nothing`
- `Result[err, ok]` — `Ok(ok) | Err(err)`
