# Nar Documentation

This repository contains the language documentation for [**Nar**](https://github.com/nar-lang) — a small,
pure functional programming language designed to be embedded into host applications via Lua.

The documentation is written in Markdown and is published as a [GitHub Pages](https://pages.github.com/)
site directly from this repository.

## Local layout

```
.
├── _config.yml          # Jekyll / GitHub Pages configuration
├── index.md             # Site landing page
├── intro.md             # Getting started
├── syntax/              # Language reference (one topic per page)
└── stdlib/              # Standard library reference (Nar.Base, Nar.Tests)
```

## Sources of truth

The syntax described in these documents is derived from:

- The Lunar compiler's parser:
  [`nar-lang/lunar/compiler/parser.lua`](https://github.com/nar-lang/lunar/blob/main/compiler/parser.lua)
- The standard library: [`nar-lang/Nar.Base`](https://github.com/nar-lang/Nar.Base)
- The test framework: [`nar-lang/Nar.Tests`](https://github.com/nar-lang/Nar.Tests)
- Worked examples: [`nar-lang/Nar.Base.Tests`](https://github.com/nar-lang/Nar.Base.Tests)

## Contributing

If something doesn't match the actual behaviour of the compiler, the parser wins — please open
an issue or pull request.

This documentation is licensed under the same terms as the rest of the Nar project (see
[`LICENSE`](./LICENSE)).
