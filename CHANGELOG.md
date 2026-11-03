# Changelog

eider-toml's versions (semver). Before 1.0 the MAJOR.MINOR is the Eider toolchain's it goes
with (`eider` in eider.toml), the PATCH its own.

## Unreleased

For Eider 0.6.

- TOML is a format for `std/serial` (eider-lang #100, #167): `toml.to_string`, `to_bytes`, `from_string`, `from_bytes`, `from_bytes_with`, and `Serializer`/`Deserializer`. Types opt in with `@[derive(Serialize, Deserialize)]`; `@[serial(...)]` shapes every format and `@[toml(...)]` this one.
- New module `toml.doc`: a complete TOML 1.0 parser into an arena tree (`doc.parse`), with the spec's redefinition rules, every string form, floats, date-times, inline tables and arrays of tables. The flattened `toml.parse`/`get`/`children`/`quote` that eider-pkg reads are unchanged.
- Writing leaves out an absent option, writes a list of records as `[[array of tables]]`, bytes as base64 text and a time as a TOML date-time. A local date or time (no UTC offset) is an error for a `time.DateTime` field.

## 0.5.0

For Eider 0.5 (`eider = "0.5"`). Nothing else changed.

## 0.4.0

For Eider 0.4 (`eider = "0.4"`). Nothing else changed.

## 0.3.0

For Eider 0.3 (`eider = "0.3"`): every plain string literal interpolates (eider-lang U29), so
the literals with braces here are raw strings or have their braces doubled. Nothing else changed.

## 0.2.0

For Eider 0.2 (`eider = "0.2"`): eider-lang's 0.2 builds programs only through LLVM (clang
17 or newer; U23) and bootstraps the compiler from a WebAssembly seed (seed/eiderc.wasm; U24).
scripts/env.sh recognises such a checkout (seed/eiderc.wasm, or seed/eiderc.c before).
Nothing else changed.

## 0.1.3

The scripts find the Eider toolchain through `eider` (eider-lang#133) instead of a fixed
eider-lang checkout: scripts/env.sh takes `$EIDER_TOOLCHAIN` (a toolchain directory or an
eider-lang checkout; eider-lang's scripts/check-tools.sh sets it to its checkout), else the
toolchain `eider toolchain show --json` names for this repository's `eider = "0.1"` (the
newest installed 0.1 release), else stops with exit 2 and says to install `eider`
(install.sh). eiderc and eider-pkg run through `eider` with that toolchain (`eider eiderc`,
`eider eider-pkg`), or directly when `eider` is not on the PATH. `EIDER_ROOT` and
`EIDER_LANG` no longer choose the toolchain, and `../eider-lang` is no longer the default.
The pinned `.vendor/<name>` fallback for a dependency applies when the toolchain is an
eider-lang checkout. Tested with the released Eider 0.1.0 and with eider-lang main. No
change to the library's code.

## 0.1.2

scripts/env.sh takes a dependency that has no `EIDER_<NAME>` and no sibling checkout from the
toolchain's pinned release in `$EIDER_ROOT/.vendor/<name>` when eider-lang's `vendor.lock`
pins it (its `scripts/vendor.sh fetch <name>` fetches and hash-checks it on first use), else
from its repository. The toolchain's old `vendor/` is no longer looked at (eider-lang #130 and
#124; no backwards compatibility). No change to the library itself (it has no dependencies, so env.sh overrides nothing); the README says where eider-lang's pinned copy lives now (`.vendor/toml`, pinned by its vendor.lock).

Needs an Eider 0.1 toolchain; the pinned copy needs one with `vendor.lock` (eider-lang #122),
and without one a dependency comes from a sibling checkout or the network.

## 0.1.1

Imports in the dotted form (eider-lang C8.26, issue #91): `import std.bytes` and `import
toml` instead of the quoted `import "std/bytes"` and `import "../toml.ei"`, in the code, the
tests, the README and the program scripts/build.sh generates to check every module.

Needs an Eider 0.1 toolchain whose compiler knows dotted imports (the eider-lang commit after
the next seed); an older 0.1 compiler stops at the first dotted import (E0001).

## 0.1.0

The first release as a repository of its own, for Eider 0.1. The code is eider-lang's
`packages/toml` as of the split (its history is kept): eider-lang now vendors this
release in `vendor/toml` for its own tools.

- `VERSION`, `eider = "0.1"`, `scripts/build.sh` and `scripts/test.sh` against an eider-lang
  checkout (`EIDER_ROOT`), refusing a toolchain of another MAJOR.MINOR.
- `scripts/version.sh` (`check`, `sync`): eider.toml agrees with VERSION; eider-lang's
  `scripts/version.sh check-siblings` and `bump` drive it.
