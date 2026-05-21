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
def native length(s: String): Int

def native cons(head: a, tail: List[a]): List[a]
```

The body is implicit: at compile time, the parser turns the declaration into a call to the host
function with the same fully‑qualified name. The runtime looks the function up in the module's
companion `init.lua` (or wherever the host configures it).

A native function declaration:

- **must** have a parameter list with all parameter names (or `_`); patterns are not allowed
  because there is no body to bind them in;
- **may** declare a return type (recommended);
- works exactly like any other function from the caller's perspective — you can partially apply
  it, pass it as a value, etc.

A native function can also be `hidden`:

```nar
def hidden native rawSlice(begin: Int, end: Int, s: String): String
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

A typical Nar package has this layout:

```
MyPkg/
├── nar.json
├── init.lua            # registers native bindings with the runtime
├── MyPkg/
│   ├── Foo.nar
│   └── Foo.lua         # Lua-side native implementations for Foo
└── ...
```

The `init.lua` file is loaded by the Lua host (or by Lunar's `lunar` runner) before any Nar
module from the package is evaluated. A typical pattern looks like:

```lua
local M = {}

function M.MyPkg_Foo_double(x)
  return x * 2
end

return M
```

How exactly natives are registered (function names, value boxing, error handling) is the runtime's
concern — see [`Nar.Base/init.lua`](https://github.com/nar-lang/Nar.Base/blob/main/init.lua) and
[Lunar's runtime](https://github.com/nar-lang/lunar/tree/main/runtime) for the canonical example.

## When to use natives

Natives exist for things Nar **cannot express on its own**:

- primitive types (`Int`, `Float`, `String`, `Char`, `List`),
- low‑level string and bitwise operations,
- access to host APIs (file I/O, the SDL bindings in `Nar.SDL`, etc.).

Application code generally shouldn't need to write its own natives — most things can be expressed
purely in Nar. But when you do, treat native declarations as a tightly controlled surface and
prefer one or two well‑typed wrappers over a sprawling FFI.
