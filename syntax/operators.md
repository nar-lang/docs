---
title: Operators & infix declarations
---

# Operators & `infix` declarations

[← Back to index](../index.html)

Nar has no built‑in operators. Every `+`, `==`, `|>`, `<>` you see is a **regular function** with
an `infix` declaration that gives it a symbolic name, an associativity, and a precedence.

## Using operators

Operators are written between their two operands:

```nar
def total: Int = 1 + 2 + 3
def hello: String = "Hello, " <> name
def doubled: List[Int] = xs |> List.map(\(n) -> n * 2)
```

To use the **underlying function** as a value (for example to pass to `List.foldl`), wrap the
operator in parentheses:

```nar
import Nar.Base.List

def sum: Int = List.foldl((+), 0, [1, 2, 3])
```

`(+)` evaluates to the binary function backing `+`.

## `infix` declarations

```nar
infix [hidden] (operator): (associativity precedence) = functionName
```

- `(operator)` — one or more characters from `! # $ % & * + - / : ; < = > ? ^ | ~ ` `.
- `associativity` — one of `left`, `right`, or `non`.
- `precedence` — an integer; higher binds tighter.
- `functionName` — the (already declared) two‑argument function the operator delegates to.
- `hidden` — keep the operator scoped to the declaring module.

```nar
infix (+) : (left  6) = add
infix (==): (non   4) = eq
infix (|>): (left  0) = apR
infix (<|): (right 0) = apL
```

## Standard library operators

The following table summarises every operator declared in the standard library. Same precedence
binds together when associativities agree; when they don't, you must add parentheses.

| Operator | Module | Assoc. | Prec. | Function | Meaning |
|---|---|---|---|---|---|
| `<\|`  | `Basics`  | right | 0 | `apL`        | apply left:  `f <\| x  ==  f(x)` |
| `\|>`  | `Basics`  | left  | 0 | `apR`        | apply right: `x \|> f  ==  f(x)` |
| `\|\|` | `Basics`  | right | 2 | `or`         | logical OR |
| `^^`   | `Basics`  | right | 2 | `xor`        | logical XOR |
| `&&`   | `Basics`  | right | 3 | `and`        | logical AND |
| `==`   | `Basics`  | non   | 4 | `eq`         | equal |
| `!=`   | `Basics`  | non   | 4 | `neq`        | not equal |
| `<`    | `Basics`  | non   | 4 | `lt`         | less than |
| `>`    | `Basics`  | non   | 4 | `gt`         | greater than |
| `<=`   | `Basics`  | non   | 4 | `le`         | less or equal |
| `>=`   | `Basics`  | non   | 4 | `ge`         | greater or equal |
| `++`   | `List`    | right | 5 | `append`     | list concatenation |
| `\|`   | `List`    | right | 5 | `cons`       | prepend |
| `<>`   | `String`  | right | 5 | `append`     | string concatenation |
| `>>>`  | `Bitwise` | left  | 5 | `shiftRightZf` | logical right shift |
| `<<`   | `Bitwise` | left  | 5 | `shiftLeft`  | left shift |
| `>>`   | `Bitwise` | left  | 5 | `shiftRight` | arithmetic right shift |
| `+`    | `Math`    | left  | 6 | `add`        | addition |
| `-`    | `Math`    | left  | 6 | `sub`        | subtraction |
| `%`    | `Bitwise` | left  | 6 | `or`         | bitwise OR |
| `^`    | `Bitwise` | left  | 6 | `xor`        | bitwise XOR |
| `*`    | `Math`    | left  | 7 | `mul`        | multiplication |
| `/`    | `Math`    | left  | 7 | `div`        | division |
| `&`    | `Bitwise` | left  | 7 | `and`        | bitwise AND |
| `<~`   | `Basics`  | left  | 9 | `composeL`   | function composition: `(g <~ f)(x)  ==  g(f(x))` |
| `~>`   | `Basics`  | right | 9 | `composeR`   | function composition: `(f ~> g)(x)  ==  g(f(x))` |

> The `|` cons operator and the `|` between `case` arms in a `select` pattern are the same
> token; their meaning is determined by context.

## Pipes & composition

The two main "glue" operators are `|>` and `<|`. They make pipelines read top‑to‑bottom or
right‑to‑left:

```nar
xs
  |> List.filter(predicate)
  |> List.map(double)
  |> List.foldl((+), 0)
```

```nar
List.foldl((+), 0)
  <| List.map(double)
  <| List.filter(predicate, xs)
```

Function composition operators `<~` and `~>` build a new function from two existing ones:

```nar
def shout: (String): String = String.toUpper ~> String.reverse
// shout = \(x) -> String.reverse(String.toUpper(x))
```

## Prefix `-`

The minus sign in front of an expression is **unary negation**, not a separate operator. It binds
tighter than any binary operator:

```nar
def x: Int = -5
def y: Int = -1 + 2          // = 1
def z: Int = -(1 + 2)        // = -3
```

There is no prefix `+` or `!` — use `not(b)` for boolean negation.

## Custom operators

You can declare your own operator in any module — pick a sensible function and assign it a symbol,
associativity and precedence:

```nar
module MyApp.Pipes

infix (>>=) : (left 1) = andThen

def andThen(callback: (a): Maybe[b], m: Maybe[a]): Maybe[b] =
  Maybe.andThen(callback, m)
```

Pick precedences carefully: matching the standard library's existing levels (0 for piping, 4 for
comparison, 6 for additive, 7 for multiplicative) usually leads to the least surprise.
