# eider-toml

The TOML subset Eider manifests and lockfiles use (`eider.toml`, `eider.lock`), for [Eider](https://github.com/klxdev/eider-lang). Pure Eider; it needs no capability.

- Comments, `[a]` / `[a.b]` headers, `[[a]]` arrays of tables, bare, quoted and dotted keys,
  basic strings, integers, booleans, arrays of strings (across lines) and inline tables.
- `toml.parse(bytes)` flattens a document into `Entry` values keyed by their full dotted key
  (the n-th `[[a]]` table is the prefix `a.<n>`), with source lines; `get`, `children` and
  `quote` (a TOML basic string) work on that.

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
