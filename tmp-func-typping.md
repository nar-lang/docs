# `:` vs `->` for function return types

## Short answer

Yes, reusing `:` for both "parameter has type" and "function returns type" creates real parsing and readability friction. Most ML‑family and modern functional/systems languages reach for `->` for the return type precisely to avoid those problems.

---

## Why most functional languages use `->` for the return type

### 1. `->` is already the type constructor for functions
In ML, Haskell, OCaml, F#, Elm, PureScript, Scala 3, Rust, Swift, etc., the *type* of a function is written with `->`:

```
Int -> Int -> Int        -- Haskell / Elm / OCaml
fn(Int, Int) -> Int      -- Rust
(Int, Int) -> Int        -- Scala / Kotlin
```

So when you write `def add(x: Int, y: Int) -> Int`, the `-> Int` part is literally the **tail of the function's type**. It composes naturally with currying and higher‑order types. With `:`, the syntactic form of the *signature* no longer mirrors the *type* — you're using two different separators for the same conceptual relationship.

### 2. `:` is the "has type" / annotation operator
`:` in most type systems means **"the term on the left has the type on the right."** A function definition is a *term*; the whole thing has *one* type. So `add : Int -> Int -> Int` reads as "the value `add` has type `Int -> Int -> Int`." Once you mix `:` into both parameter annotations *and* the return slot, you blur a single, clean rule into two roles.

### 3. Parsing / grouping ambiguity (the concrete pitfall)
With `:` reused, consider:

```nar
def f(x: Int, y: Int): List[Int -> Int] = ...
def mkUser(name: String): { name: String, age: Int } = ...
```

The second `:` inside the record type is the same token as the "return type" `:`. A parser handles it, but a reader has to mentally track nesting depth to know which `:` is the return separator. With `->`, the return arrow is unambiguous against any `:` inside parameter or return types.

It gets worse with lambdas: Nar already uses `->` to mean "lambda body follows" (`\(x: Int): Int -> x + 1`), so the signature mixes `:` (return type) *and* `->` (body) within a few characters of each other. ML‑family languages avoid that collision by using `->` for the return type and `=>`/`=` for the body.

### 4. Currying and partial application read more naturally with `->`
In a curried world, `(x: Int, y: Int) -> Int` is morally the same as `Int -> Int -> Int`. The return arrow is *the same arrow* that separates curried argument groups. Using `:` hides that equivalence and makes the language look more imperative/Pascal‑like than it actually is.

### 5. Historical precedent
- **ML (1973)** chose `->` for function types and `:` for "has type." Every descendant (OCaml, SML, Haskell, Elm, PureScript, F#, Roc, Gleam, Grain…) inherited that split.
- **Pascal** and its descendants (TypeScript, Python, Kotlin, Scala 2, Swift parameter lists) use `:` for return types because they treat function declarations syntactically, not as values whose type happens to be a function type. Nar's `(x: Int, y: Int): Int` sits in that Pascal/TypeScript/Python tradition.

So Nar's choice is internally consistent — it just sits on the "declaration‑oriented" side of the fence rather than the "expression‑oriented / type-as-value" side that ML‑family languages live on.

---

## Concrete pitfalls in Nar's current choice

1. **Visual collision with records.** A function returning a record `): { x: Int, y: Int } =` packs three `:` tokens with three different meanings (return type marker, two field annotations) onto one line.

2. **Lambda signatures mix two arrows.** `\(x: Int, y: Int): Int -> x + y` — `:` for the return type, `->` immediately after for the body. ML‑style would use one arrow consistently.

3. **`def`-header type vs. function-value type look different.** When you move a function into a `let` binding or query its type, the notation has to switch from `:` to `->`. With ML‑style syntax it's the same arrow everywhere.

4. **Error-recovery friction.** A missing `)` in the parameter list can cascade — the parser may think the return‑type `:` is still a parameter annotation, producing confusing diagnostics.

5. **Nested function types in parameters.** A parameter that is itself a function type (`f: (Int): Int`) genuinely looks ambiguous to a human reader, even if the grammar disambiguates it.

---

## The flip side — why `:` is defensible

- Familiar to anyone coming from Python, TypeScript, Kotlin, Swift, Scala 2, Pascal.
- For declaration‑oriented languages, `(x: T, y: T): R` reads naturally as "this function, with these parameters, has type R."
- Saves one token shape, keeping the grammar surface smaller for newcomers.

Since Nar is curried, pure, and ML‑adjacent (ADTs, pattern matching, `|>`, `<>`), the `->` convention would arguably fit its semantics better — but the `:` choice is a legitimate stylistic pick, not a bug. The main practical cost is the parser/reader work around nested `:` in record and function types, and the loss of a single uniform "function arrow" between value‑level signatures and type‑level expressions.
