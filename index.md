---
title: Nar Language
---

# The Nar Programming Language

**Nar** is a small, statically typed, pure functional programming language with a minimal surface,
algebraic data types, and an "if it compiles, it works" feeling. Nar is designed to be
**embedded** — the reference
implementation, [Lunar](https://github.com/nar-lang/lunar), compiles Nar source to bytecode that
runs on top of Lua, so you can drop Nar into any application that already runs Lua scripts.

```nar
module Hello

import Nar.Base.String exposing (toUpper)

def greet(name: String) -> String =
  "Hello, " <> toUpper(name) <> "!"

def main: String =
  greet("world")
```

## Why Nar?

- **Tiny and predictable.** A handful of keywords, no statements — just expressions.
- **Pure.** Functions are just functions; no surprises from hidden mutations.
- **Strong static types.** With type inference; you only annotate what you want to.
- **Algebraic data types and pattern matching** as first‑class building blocks.
- **Easy embedding.** The runtime sits on top of Lua, which itself embeds almost anywhere.

## Where to go next

### 1. Get up and running
- [Introduction & Getting Started](./intro.html)

### 2. Language reference

| Topic | Page |
|---|---|
| Comments and lexical structure | [syntax/comments](./syntax/comments.html) |
| Literals & primitive values | [syntax/values](./syntax/values.html) |
| Identifiers & naming | [syntax/identifiers](./syntax/identifiers.html) |
| Functions & lambdas | [syntax/functions.html](./syntax/functions.html) |
| `if`, `let`, `select` | [syntax/control-flow](./syntax/control-flow.html) |
| Tuples | [syntax/tuples](./syntax/tuples.html) |
| Records | [syntax/records](./syntax/records.html) |
| Lists | [syntax/lists](./syntax/lists.html) |
| Custom types (unions) | [syntax/types](./syntax/types.html) |
| Type aliases | [syntax/aliases](./syntax/aliases.html) |
| Patterns | [syntax/patterns](./syntax/patterns.html) |
| Type expressions | [syntax/type-expressions](./syntax/type-expressions.html) |
| Operators & `infix` | [syntax/operators](./syntax/operators.html) |
| Modules & `import` | [syntax/modules](./syntax/modules.html) |
| `native` declarations | [syntax/native](./syntax/native.html) |

### 3. Standard library and packages

Per-package documentation lives alongside each package's source:

- Standard library: [`Nar.Base`](https://github.com/nar-lang/Nar.Base)
- Test framework: [`Nar.Tests`](https://github.com/nar-lang/Nar.Tests)
