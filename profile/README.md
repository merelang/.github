# merelang

A small ML-family programming language with region-based memory (no GC),
effects as ordinary capability values, and **five backends**: interpreter,
C, LLVM IR, Wasm, and RV32IM. The first four are held to each other by a
parity suite that compares their output byte for byte; the fifth boots
under QEMU — the one implementation in the stack nobody here wrote.

The name **Mere** is Old English for "lake" — the region metaphor (a
body bounded from its surroundings), the minimal ML-family ring, and a
modest "just a …" nuance.

## Repositories

| repo | what |
|---|---|
| [**mere**](https://github.com/merelang/mere) | reference compiler / runtime / examples / docs (OCaml) |
| [**mere-vscode**](https://github.com/merelang/mere-vscode) | VS Code extension — a thin client over `mere lsp` |

The policy is **dogfood-first**: nothing graduates out of `mere/contrib/`
until there is an external consumer asking for independent versioning.
`mere-vscode` is separate because the coupling is a protocol rather than a
filesystem — the extension does not read a line of the compiler's source,
it starts `mere lsp` and speaks LSP.

## Status

- **2526 tests**, cross-backend parity over 112 programs, and about thirty
  differential gates in CI that compare Mere against external oracles
  (protoc, grpcurl, graphql-js, python `h2` / `hpack`, html5lib, QEMU,
  Blargg's Game Boy suite, and others).
- **Self-hosting at a fixpoint**: the Mere-in-Mere compiler, compiled by
  itself and run as Wasm, emits byte-identical output to the reference.
- **A fifth backend, and an operating system on it**: `mere -rv` emits a
  flat RV32IM binary with no external assembler or linker, and `--bare`
  hands the program the machine instead of a host — raw memory arrives as
  an unforgeable window capability and traps are ordinary closures. On
  that: a preemptive scheduler, a shell, a syscall boundary, and the
  self-hosted compiler running as a user process **on a RISC-V CPU also
  written in Mere**, emitting WAT byte-identical to the native one.
- **Editor support out of the compiler's own answers**: diagnostics,
  hover, definition, completion, outline, formatting, semantic tokens,
  references and rename, served by `mere lsp`.

## What's distinctive

- **Memory model**: strategies (owned / borrowed / region / view / stack)
  chosen at the type level, not auto-inferred. Region values are
  `Trivial[R]` by default; `with` handles `Drop` types in LIFO scope order.
- **Effects as values**: capabilities are passed as ordinary function
  arguments — no monad-of-the-month, no effect rows.
- **Borrow annotations**: four modes (`&R T` / `&mut R T` /
  `&shared write R T` / `&exclusive R T`) covering the full 10-pair
  conflict matrix, checked statically.
- **Backends that are held to each other**: anything the interpreter runs
  also runs as a native binary, as LLVM IR and as Wasm, compared byte for
  byte — and where they legitimately differ, the difference is pinned so
  it keeps being asked about rather than tolerated.

## Getting started

```sh
curl -fsSL https://raw.githubusercontent.com/merelang/mere/main/scripts/install.sh | sh
mere examples/factorial.mere
```

Docs, tutorial and a Wasm playground: **[merelang.org](https://merelang.org/)**.

## License

All merelang repositories ship under the **MIT license**.
