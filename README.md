# eider-toml

TOML for [Eider](https://github.com/klxdev/eider-lang): a `std/serial` format, a complete TOML 1.0 document tree, and the flattened subset eider-pkg reads. Pure Eider; it needs no capability.

- **Typed values** (`import toml`, eider-lang #167): a type with `@[derive(Serialize, Deserialize)]`
  goes to TOML with `toml.to_string(x)` and comes back with
  `let r: Result[Config, serial.Error] = toml.from_string(text)`. `@[serial(...)]` shapes the
  mapping for every format and `@[toml(...)]` (`name`, `skip`, `default`, `rename_all`, `tag`,
  `unknown`) for this one. Records are tables, lists of records `[[arrays of tables]]`, an absent
  option is left out, bytes are base64 text, a `time.DateTime` is a TOML date-time. Errors name
  their place (`servers[1].port`).
- **The document** (`import toml.doc`): `doc.parse(bytes)` reads all of TOML 1.0 into a tree
  (any depth of arrays and tables, dotted keys, every string form, floats, date-times) and checks
  the spec's rules for redefinition.
- **The flattened subset** eider-pkg reads (`eider.toml`, `eider.lock`): `toml.parse(bytes)`
  flattens a document into `Entry` values keyed by their full dotted key (the n-th `[[a]]` table is
  the prefix `a.<n>`), with source lines; `get`, `children` and `quote` work on that.

Errors carry the line and column.

Used by: eider-lang (eider-pkg).

## Using it

In your package's `eider.toml`:

```toml
[deps.toml]
git = "https://github.com/klxdev/eider-toml.git"
version = "0.1.0"
# pure: granted nothing (no `uses`)
```

then `eider fetch` and `import toml`.

## Developing

```sh
sh scripts/build.sh     # fetch the dependencies, check every module
sh scripts/test.sh      # build, then run tests/*.ei against their .out files
EIDER_TOOLCHAIN=../eider-lang sh scripts/test.sh   # the same, against an eider-lang checkout
```

The scripts use the toolchain [`eider`](https://github.com/klxdev/eider-lang/blob/main/docs/site/tooling/eider.md)
picks: `$EIDER_TOOLCHAIN` (an installed toolchain's directory, or an eider-lang checkout for
unreleased work), else the installed release for `eider.toml`'s `eider = "0.1"` (what
`eider toolchain show` prints here). Install `eider` and the latest toolchain with
eider-lang's `install.sh --toolchain` (from its latest release); without either, the
scripts stop with exit 2 and say so. The toolchain's `VERSION` must have this package's
MAJOR.MINOR (the `eider` field of `eider.toml`), else they stop with exit 2 and say which
release to use. eiderc and eider-pkg run through `eider` (`eider eiderc ...`, `eider eider-pkg
...`, with that toolchain), or directly when `eider` is not on the PATH. It has no dependencies beyond the toolchain's `core` and `std`. eider-lang pins a release of it in its `vendor.lock` and fetches it into `.vendor/toml` for its own tools (`scripts/vendor.sh`, which checks the content hash); that copy must stay byte-identical to a tagged release here.

eider-lang's `scripts/verify.sh` builds and tests this repository against its own checkout
when it is next to it (`scripts/check-tools.sh`).

## Versions

`VERSION` and `eider.toml` hold this package's version (semver). Before 1.0 its MAJOR.MINOR
follows the Eider toolchain it goes with (`eider = "0.1"`) and its PATCH is its own;
CHANGELOG.md lists the releases. A release is tagged `v<version>`, which is what
`version = "..."` in a dependent's `eider.toml` resolves to.
