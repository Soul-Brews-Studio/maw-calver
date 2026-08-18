# maw-calver

CalVer version scheme for the maw fleet. Extracted from
[maw-rs](https://github.com/Soul-Brews-Studio/maw-rs) (repo split phase 3);
originally ported from maw-js `scripts/calver.ts`.

Pure, deterministic CalVer arithmetic — no git or package IO. Behavior is
locked by the portable fixture file `tests/fixtures/calver.fixtures.json`,
shared with maw-js.

## Scheme

Day-based CalVer (TZ = Bangkok wall clock):

```
stable:  v<YY>.<M>.<DD>                 one per day
alpha:   v<YY>.<M>.<DD>-alpha.<HMM>     HMM = H×100+M
beta:    v<YY>.<M>.<DD>-beta.<HMM>      independent channel
```

`HMM` is wall-clock time as a decimal integer with no leading zero (18:30 →
`1830`, 09:05 → `905`). If `HMM` ≤ the highest existing suffix for the same
base+channel, the version advances to the next calendar day
(`next_calendar_base`).

## Usage

Consume as a Cargo git dependency (not published to crates.io):

```toml
[dependencies]
maw-calver = { git = "https://github.com/Soul-Brews-Studio/maw-calver", rev = "<sha>" }
```

Entry point: `compute_version(args, tags, package_version)` — see `src/lib.rs`.

## Release model

This repo publishes GitHub releases by annotated tag, **not** crates.io.

- Tag format: `v<YY>.<M>.<D>-alpha.<HMM>` (or `beta` channel).
- Consumers must pin by full commit SHA for immutability; tags are mutable names.
- Example consumption in downstream `Cargo.toml`:

```toml
[dependencies]
maw-calver = { git = "https://github.com/Soul-Brews-Studio/maw-calver", rev = "<40-hex commit SHA>", version = "=26.8.18-alpha.1329" }
```


## Development

```bash
cargo fmt --all -- --check
cargo test --workspace --locked --no-fail-fast
cargo clippy --workspace --all-targets --locked -- -D warnings
```

CI runs these commands with the pinned Rust toolchain from
`rust-toolchain.toml`. `forbid(unsafe_code)`, clippy pedantic clean, Rust edition
2021.

## License

BUSL-1.1 — see [LICENSE](LICENSE).
