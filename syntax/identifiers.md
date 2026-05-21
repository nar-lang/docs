---
title: Identifiers & naming
---

# Identifiers & naming

[← Back to index](../index.html)

## Lower‑case vs. upper‑case

Nar follows the same naming rule as Elm: **case carries meaning**.

| Starts with | Used for |
|---|---|
| Lower‑case letter | values, functions, type parameters, record fields, pattern bindings |
| Upper‑case letter | type names, alias names, type **constructors** (a.k.a. options), module name segments |

```nar
def myValue: Int = 1                  // lower-case → value
type Color = Red | Green | Blue       // upper-case → type and constructors
type List[a] = ...                    // lower-case `a` → type parameter
def first(point: { x: Int }): Int =   // lower-case `x` → field
  point.x
```

This rule is enforced syntactically by the parser:

- A type parameter name **must** start with a lower‑case letter.
- In a `select` pattern, an identifier starting with an upper‑case letter is read as a constructor
  (option) pattern; one starting with a lower‑case letter is read as a binding name.

## Allowed characters

After the first character, an identifier may contain any combination of:

- letters (`a‑z`, `A‑Z`);
- digits (`0‑9`);
- `_` (underscore);
- `` ` `` (back‑tick).

```nar
def x: Int = 1
def index_2: Int = 2
def helper`v2(x: Int): Int = x + 1
```

`_` by itself is not an identifier — it is the [wildcard pattern](./patterns.html).

## Qualified names

A dot inside an identifier introduces a **module qualifier**. Qualifications can be arbitrarily
deep:

```nar
import Nar.Base.List

def xs: List[Int] = Nar.Base.List.range(1, 10)
//                    ^^^^^^^^^^^^^^^^^^^^^^ — qualified value name
```

Qualifications are only legal where a qualified identifier is expected (type names, expression
references, import statements). They are **not** allowed for type parameters or for field names
inside a record literal/pattern.

The simple name in scope (after `import` resolution) and the fully qualified name both refer to the
same thing. See [Modules & `import`](./modules.html) for the full story.

## Operator names

A symbolic operator (e.g. `++`, `<>`, `|>`) is a regular function exposed under a normal
lower‑case name (`append`, `apR`, …) and **declared** as an infix alias with `infix`. To use the
operator's underlying function as a value, wrap it in parentheses:

```nar
def sum: List[Int] = List.foldl((+), 0, [ 1, 2, 3 ])
```

See [Operators & `infix`](./operators.html).

## Reserved names

The keywords listed in [the lexical structure page](./comments.html#reserved-keywords) are not
available as identifiers.
