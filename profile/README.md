# merelang

A small ML-family programming language with explicit region-bound
memory, capability-passing effects, and feature-parity codegen to
**interp + C + LLVM + Wasm**.

The name **Mere** is Old English for "lake" — the region metaphor (a
body bounded from its surroundings), the minimal ML-family ring, and a
modest "just a …" nuance.

## Repositories

| repo | what |
|---|---|
| [**mere**](https://github.com/merelang/mere) | reference compiler / runtime / examples / docs (OCaml) |

More repos may be split out as the language stabilises (a tree-sitter
grammar and a dedicated DOM bindings library are the obvious next
candidates), but the policy is **dogfood-first**: nothing graduates
out of `mere/contrib/` until there is an external consumer asking for
independent versioning.

## Status

- **1580 tests** across interp + C + LLVM + Wasm. 16 realistic
  examples (~2500 LoC; toy_sql.mere alone is 1165 LoC) match
  `diff = 0 PERFECT` across all four backends.
- **Phase 48 (frontend FFI MVP)** shipped 2026-06-25 — Mere code now
  runs interactively in the browser via DOM bindings + an exported
  Wasm function table that lets JavaScript dispatch back into Mere
  closures. See [counter demo](https://github.com/merelang/mere/blob/main/contrib/site/playground/counter.mere).
- **`mere fmt`** is the built-in source formatter with `--check` for
  CI / pre-commit (Phase 47).

## What's distinctive

- **Memory model**: 5 strategies (owned / borrowed / region / view /
  stack) chosen at the type level, not auto-inferred. Region values
  are `Trivial[R]` by default; `with` handles `Drop` types in LIFO
  scope order.
- **Effects as values**: capabilities are passed as ordinary function
  arguments — no monad-of-the-month, no effect rows. The bucket-brigade
  is intentional and shape-of-signature-aliased.
- **Borrow annotations**: four modes (`&R T` / `&mut R T` /
  `&shared write R T` / `&exclusive R T`) cover the full 10-pair
  conflict matrix, checked statically.
- **4-backend feature parity**: anything in interp also runs as C
  native binary, LLVM IR, and Wasm — verified by `diff = 0` on the
  same example set.

## Getting started

```sh
git clone git@github.com:merelang/mere
cd mere
dune build
dune exec mere -- examples/factorial.mere   # interpreter
dune exec mere -- -c examples/factorial.mere | clang -x c - -o fact && ./fact  # C
dune exec mere -- -w examples/factorial.mere > fact.wat && wat2wasm fact.wat   # Wasm
```

Full tutorial: [docs/tutorial.md](https://github.com/merelang/mere/blob/main/docs/tutorial.md).

## License

All merelang repositories ship under the **MIT license**.
