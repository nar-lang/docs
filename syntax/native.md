---
title: Native declarations
---

# `native` declarations

[← Back to index](../index.html)

Because Nar runs on top of Lua, the language has a small "foreign function interface": you can
declare a value, function, or alias as `native` to mean "the host runtime provides this". The Nar
side states the **types**; the Lua side provides the **implementation**.

## Native functions

```nar
def native length(s: String) -> Int

def native cons(head: a, tail: List[a]) -> List[a]
```

The body is implicit: at compile time, the parser turns the declaration into a call to the host
function with the same fully‑qualified name. The runtime looks the function up in the module's
companion `init.lua` (or wherever the host configures it).

A native function declaration:

- **must** have a parameter list with all parameter names (or `_`); patterns are not allowed
  because there is no body to bind them in;
- **must** have a full type signature whenever any part of it is annotated — every parameter
  type *and* the return type. Partial signatures are rejected:
  - `def native foo(x: Int)` fails normalization with `missing return type annotation`;
  - `def native foo(x) -> Int` fails the type checker because the parameter type cannot be
    resolved.
  In practice every useful native function has fully typed parameters and a declared return
  type — that is the whole point of `native`, since there is no body for inference to fall back
  on.
- works exactly like any other function from the caller's perspective — you can partially apply
  it, pass it as a value, etc.

A native function can also be `hidden`:

```nar
def hidden native rawSlice(begin: Int, end: Int, s: String) -> String
```

## Native values

A native value has no parameters; the runtime provides a constant (or any value of the declared
type):

```nar
def native epoch: Float

def native maxInt: Int
```

## Native aliases

```nar
alias native Int
alias native Float
alias native String
alias native Char
alias native List[a]
```

A native alias has **no `=` right‑hand side**: there is no Nar‑side definition because the
representation lives in the host runtime. All standard primitive types are declared this way.

## Companion Lua files

A package that exposes natives ships an `init.lua` next to its `nar.json`. Nar itself does not
prescribe any package-internal directory layout: `.nar` files can live wherever the package
author chooses, and there is no `<PkgName>/<SubModule>.lua` shim convention. Everything the Lua
side needs to provide is registered from one entry point.

The Lua host (Lunar's `lunar` runner, or any embedder using `lunar.runtime`) loads `init.lua`
once before any Nar module from the package is evaluated. The file is **executed for its side
effects** — it does not need to `return` a module table. Inside it, each native is registered
against its fully‑qualified Nar name with `rt:registerDef(moduleName, defName, fn, arity)`.

The canonical example is [`Nar.Base/init.lua`](https://github.com/nar-lang/Nar.Base/blob/main/init.lua).
A representative excerpt:

```lua
-- init.lua at the root of the package
local rt = require("lunar.runtime")

rt:registerDef("Nar.Base.Math", "add", function(rt, x, y)
    return rt:makeInt(x.value + y.value)
end, 2)

rt:registerDef("Nar.Base.String", "length", function(rt, s)
    return rt:makeInt(#rt:toString(s))
end, 1)

rt:registerDef("Nar.Base.Char", "toCode", function(rt, char)
    return rt:makeInt(char.value)
end, 1)
```

Notes on what's going on:

- The first two arguments to `registerDef` are the Nar module name (as declared by its
  `module ...` directive) and the bare definition name. Together they form the fully‑qualified
  name the compiler emits for a `def native` call.
- The function receives the runtime as its first parameter and the Nar arguments after that.
  It must return a runtime `Object` (use the `rt:makeInt`, `rt:makeFloat`, `rt:makeString`,
  `rt:makeBool`, `rt:makeList`, `rt:makeUnit`, … helpers).
- The trailing integer is the arity — how many Nar arguments the function takes. It must match
  the parameter list on the Nar side.
- Use the `rt:toString`, `rt:toInt`, `rt:toBool`, `rt:toList` helpers to unwrap incoming Nar
  values. Primitive boxes also expose `.value` directly.

See [Lunar's runtime](https://github.com/nar-lang/lunar/tree/main/runtime) for the full set of
helpers and the `Object` model.

## When to use natives

Natives exist for things Nar **cannot express on its own**:

- primitive types (`Int`, `Float`, `String`, `Char`, `List`),
- low‑level string and bitwise operations,
- access to host APIs (file I/O, the SDL bindings in `Nar.SDL`, etc.).

Application code generally shouldn't need to write its own natives — most things can be expressed
purely in Nar. But when you do, treat native declarations as a tightly controlled surface and
prefer one or two well‑typed wrappers over a sprawling FFI.
