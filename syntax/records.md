---
title: Records
---

# Records

[← Back to index](../index.html)

A **record** is a fixed collection of named, typed fields — Nar's structural alternative to a class
or struct.

## Record literals & types

```nar
def origin: { x: Int, y: Int } =
  { x = 0, y = 0 }

def alice: { name: String, age: Int } =
  { name = "Alice", age = 30 }
```

In a **type**, fields are written `name: Type` and separated by `,`. In a **value**, fields are
written `name = expr` and separated by `,`. A record type with the same set of field names but in
a different order is the same type.

There is also an "extensible" record type used in some standard‑library signatures (`{ ext | a: T,
... }`), but in everyday code you'll mostly write closed record types.

## Field access

Use `.` to read a field:

```nar
def name: String = alice.name
def x: Int       = origin.x
```

Each field name also has a corresponding **accessor function** written `.fieldName`:

```nar
import Nar.Base.List

def names(people: List[{ name: String }]): List[String] =
  List.map(.name, people)
```

The accessor `.name` is just a function `(r) -> r.name`, useful for `map` / `filter` etc.

## Record update

To produce a new record with a few fields changed, use the `{ original | field = newValue, ... }`
form:

```nar
def older: { name: String, age: Int } =
  { alice | age = alice.age + 1 }
```

- The original record is **not modified** — Nar values are immutable.
- Only listed fields are changed; everything else is copied.
- Listed fields must already exist on the record (you cannot add new fields with update).

You can update several fields at once:

```nar
def moved: { x: Int, y: Int } =
  { origin | x = 10, y = 20 }
```

## Destructuring

A record pattern lists the field names you care about between `{ ... }`:

```nar
def fullName({ first, last }: { first: String, last: String }): String =
  first <> " " <> last
```

You don't have to mention every field — the pattern only binds the named ones. Field names in the
pattern must exist on the record being matched.

## Type aliases for records

Records are most pleasant when you give them a name with `alias`:

```nar
alias Point = { x: Int, y: Int }

def origin: Point = { x = 0, y = 0 }
```

See [type aliases](./aliases.html).

## Comparison

Two records of the same type are equal iff all of their fields are pair‑wise equal. There is no
"reference identity" — equality is structural.
