---
title: Modules & imports
---

# Modules & `import`

[← Back to index](../index.html)

Every `.nar` file is exactly one **module**. The module's name is taken from the
`module` directive inside the file — **not** from the file's path and **not**
from the enclosing package's name.

## `module` header

```nar
module Some.Dotted.Name
```

- The name segments are dotted upper‑case identifiers.
- This declared name is the module's identity. The compiler keys modules by it,
  reports collisions on it, and uses it everywhere it needs to qualify a
  definition.
- The file path on disk is **only** used for diagnostics (e.g. where to point
  parse errors). It plays no role in name resolution.

### Module name vs. package name

A package is a directory with a `nar.json` manifest:

```json
{
  "name": "Nar.Base",
  "repository": "github.com/nar-lang/Nar.Base",
  "dependencies": []
}
```

The package name and the module name are **independent**. A package called
`Foo.Bar` is free to contain a module called `Anything.You.Like`. The compiler
will happily load it.

### File‑layout convention (not enforced)

By convention, a module called `A.B.C.D` that lives in a package called `A.B`
is stored at `C/D.nar` inside that package — i.e. the package name is the
common prefix and the rest of the dotted module name becomes the on‑disk path.

This is *only* a convention to help humans find files. The compiler doesn't
check it; you can put any module in any `.nar` file in any directory and it
will still be located by its declared `module` name.

A module's "public surface" is everything declared at top level **except**
items prefixed with `hidden`.

## `import` is (mostly) optional

This is a deliberate, central feature of Nar: **you do not need an `import` to
use a definition from another module**. The compiler resolves identifiers by
trying several strategies in order, and only falls back to the next strategy if
the previous one didn't find a unique match.

For a reference `x` (or `M.x`, or `A.B.x`) inside some module:

1. **Local binding.** A `let`, lambda parameter, function argument, or pattern
   binding in the current scope wins outright.
2. **Current module.** A top‑level definition in the same module wins next.
3. **Imported modules.** If any `import` in the current module exposes the
   bare name `x` (either through `exposing` or `exposing *`), or exposes it
   under a qualifier `M.x` derived from the imported module's full name or its
   alias / short name, the lookup is delegated to that imported module.
4. **Any module by fully qualified name.** When the reference looks like
   `A.B.x`, the compiler tries to find a module literally named `A.B`
   (anywhere in the compilation set, in any package) and resolve `x` inside
   it. No `import` required.
5. **Any module by short / suffix name.** If `A.B.x` doesn't match a full
   module name, the compiler looks for any module whose dotted name *ends
   with* `.A.B` and resolves `x` there. Useful for short‑form qualification
   like `List.map` where the actual module is `Nar.Base.List`.
6. **Capitalised bare name as a module name.** A bare reference like
   `List.map` is allowed even if there's no module literally called `List`:
   the compiler will look for any module whose name equals `List` or ends in
   `.List` and try to resolve `map` there.
7. **Bare definition name across all modules.** As a last resort, an
   unqualified `x` is searched for in every module that's part of the
   compilation. If exactly one module exports a matching definition, it
   wins.

If two or more of these strategies find different definitions for the same
reference, the compiler reports an **ambiguous identifier** error. The fix is
either to qualify the reference more (e.g. write `Nar.Base.List.map` instead of
`map`) or to add an `import` that makes your intent explicit.

The fully qualified name of a definition (`Nar.Base.List.map`) is therefore
**always** reachable from anywhere — there is no concept of a module being
"hidden" from another module by virtue of not having been imported. `hidden`
on individual declarations is the only access control mechanism.

## What `import` is actually for

Given the above, `import` is a *naming convenience*, not a dependency
declaration. It exists to let you:

- **Shorten** references: `import Nar.Base.List` lets you write `List.map`
  rather than `Nar.Base.List.map` (the short form would still work in most
  cases, but `import` documents intent and prevents ambiguity).
- **Drop the qualifier entirely** with `exposing`:
  ```nar
  import Nar.Base.List exposing (map, filter)
  ```
  now `map` and `filter` are usable unqualified.
- **Rename** with `as`:
  ```nar
  import Nar.Base.List as L
  ```
  enables `L.map`, etc.
- **Disambiguate** when two modules export the same name and Nar would
  otherwise report an ambiguity.
- **Document** which other modules your module logically depends on.

### `import` forms

`import` clauses must appear immediately after the module header (before any
`def` / `type` / etc.).

```nar
import Some.Module
import Some.Module as Local
import Some.Module exposing (a, b, (++))
import Some.Module as Local exposing (a, b)
import Some.Module exposing *
```

| Form | Effect |
|---|---|
| `import M` | Reach exported names as `M.foo` (and as the trailing short name `M.foo` where `M` is the last dotted segment). |
| `import M as L` | Reach things as `L.foo` instead. |
| `import M exposing (foo, bar)` | Bring `foo` and `bar` into the local scope unqualified, in addition to the qualified forms. |
| `import M exposing ((+), (++))` | Operators are spelled in parentheses inside `exposing`. |
| `import M exposing *` | Bring **everything** exported by `M` into local scope. |

An `import` can combine `as` and `exposing`:

```nar
import Nar.Base.List as L exposing (map, filter)
```

After this, `L.foldl`, `map` and `filter` all work; everything else stays
reachable as `L.something` (and, as always, as `Nar.Base.List.something`).

### Exposing operators

To expose an operator, write it between parentheses:

```nar
import Nar.Base.List exposing ((++), (|))
```

### Re‑exports

There is no explicit "re‑export" syntax. To re‑export, define a thin wrapper
or alias in your own module.

## `hidden`

A top‑level declaration prefixed with `hidden` is invisible outside its module
— it is removed from the set of names that any of the resolution strategies
above will consider, including resolution via fully qualified name:

```nar
module Math.Stats

def mean(xs: List[Float]): Float =
  total(xs) / Math.toFloat(List.length(xs))

def hidden total(xs: List[Float]): Float =
  List.foldl((+), 0.0, xs)
```

`hidden` works on any of `def`, `type`, `alias`, `infix`, and on individual
constructors of a `type`.

## Cyclic imports

Two modules cannot import one another in a cycle. Restructure shared code into
a third module that both can use.

## Where do modules actually come from?

The compiler is handed a flat set of source files (`fileName -> content`). It
parses each one and keys the resulting module by the name in its `module`
directive. Every module that the compilation pipeline is given participates in
name resolution as described above, regardless of which package it came from
or which directory it lived in. Native shims (`.lua` files) are loaded by the
Lua runtime according to its own search path; see [native](./native.html).
