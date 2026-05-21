---
title: Modules & imports
---

# Modules & `import`

[← Back to index](../index.html)

Every `.nar` file is exactly one **module**. The module declaration is the first non‑comment line
in the file.

## `module` header

```nar
module Some.Dotted.Name
```

- The name segments are dotted upper‑case identifiers.
- The convention is that the dotted name mirrors the file path inside the package: a module called
  `Foo.Bar.Baz` lives at `Foo/Bar/Baz.nar`.
- A package is a directory with a `nar.json` manifest:
  ```json
  {
    "name": "Nar.Base",
    "repository": "github.com/nar-lang/Nar.Base",
    "dependencies": []
  }
  ```

A module's "public surface" is everything declared at top level **except** items prefixed with
`hidden`.

## `import` statements

`import` clauses must appear immediately after the module header (no `def` / `type` / etc. before
them).

```nar
import Some.Module
import Some.Module as Local
import Some.Module exposing (a, b, (++))
import Some.Module as Local exposing (a, b)
import Some.Module exposing *
```

| Form | Effect |
|---|---|
| `import M` | Use `M.foo` to reach exported names. |
| `import M as L` | Same, but reach things via `L.foo` instead. |
| `import M exposing (foo, bar)` | Bring `foo` and `bar` into the local scope unqualified. |
| `import M exposing ((+), (++))` | Operators are spelled in parentheses inside `exposing`. |
| `import M exposing *` | Bring **everything** exported by `M` into local scope. |

An `import` can combine `as` and `exposing`:

```nar
import Nar.Base.List as L exposing (map, filter)
```

After this, `L.foldl`, `map` and `filter` all work; everything else is reachable as `L.something`.

The fully‑qualified name (`Nar.Base.List.map`) is **always** available regardless of how (or
whether) you imported the module.

### Exposing operators

To expose an operator, write it between parentheses:

```nar
import Nar.Base.List exposing ((++), (|))
```

### Re‑exports

There is no explicit "re‑export" syntax. To re‑export, define a thin wrapper or alias in your own
module.

## `hidden` and `exposing`

A top‑level declaration prefixed with `hidden` is invisible outside its module, even via fully
qualified name:

```nar
module Math.Stats

def mean(xs: List[Float]): Float =
  total(xs) / Math.toFloat(List.length(xs))

def hidden total(xs: List[Float]): Float =
  List.foldl((+), 0.0, xs)
```

`hidden` works on any of `def`, `type`, `alias`, `infix`, and on individual constructors of a
`type`.

## Cyclic imports

Two modules cannot import one another in a cycle. Restructure shared code into a third module that
both can import.

## Search path

The Lunar runtime resolves `import M.N.P` by looking for `M/N/P.nar` inside the current package
and inside every package listed in `nar.json`'s `dependencies`. Native shims (`.lua` files) are
loaded by the Lua runtime according to its own search path; see [native](./native.html).
