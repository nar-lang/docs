---
title: Standard library — Debug
---

# `Nar.Base.Debug`

[← Back to index](../index.html)

Debugging helpers and runtime type introspection. These are useful while developing but should
typically be removed from production code — `log` produces side effects (printing) and `todo`
crashes when reached.

## Functions

```nar
def native toString(x: a): String
def native log(msg: String, a: a): a
def native todo(msg: String): a
def native getType(x: a): Type
```

`log(msg, a)` prints `msg` together with a representation of `a` and then returns `a` unchanged —
ideal for sprinkling into expressions:

```nar
def evens(xs: List[Int]): List[Int] =
  xs
    |> Debug.log("input")
    |> List.filter(\(n) -> modBy(2, n) == 0)
    |> Debug.log("output")
```

`todo(msg)` has type `a` (any type), but evaluating it raises a runtime error. Use it as a
placeholder for branches you haven't implemented yet:

```nar
def parseColor(s: String): Color =
  select s
    case "red"   -> Red
    case "green" -> Green
    case _       -> Debug.todo("unsupported colour: " <> s)
  end
```

## The `Type` enum

`Debug.getType(x)` returns one of:

```nar
type Type
  = TypeUnknown
  | TypeUnit
  | TypeInt
  | TypeFloat
  | TypeString
  | TypeChar
  | TypeRecord
  | TypeTuple
  | TypeList
  | TypeOption
  | TypeFunction
  | TypeClosure
  | TypeNative
```

It reflects the *runtime* shape of a value rather than its compile‑time type, and is mostly useful
when writing generic infrastructure code.
