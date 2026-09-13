# CLAUDE.md

Guidance for Claude Code working in **QuetzaLib-PWA**.

## What this repo is

The installable browser build of QuetzaLib: build tooling (`tools/`),
browser-only shims (`shim/`), and the generated `dist/` output.

## The one rule that matters here

**The app is not in this repo.** `lib/` lives in
[QuetzaLib-APP](https://github.com/ZYDRAXYL/QuetzaLib-APP) and serves Android,
browser, and desktop from one source tree. PWA is a *build target* of that tree,
not a fork of it.

The web/native split is already handled inside APP, through conditional exports:
`local_image_platform.dart`, `app_image_impl_*.dart`, `local_image_size_*.dart`,
and `app_update_section_*.dart` each pick an `_io` or `_web` implementation. On
web, `sqflite_common_ffi_web` serves the *same* sqflite API over a real SQLite
file in IndexedDB, so `DatabaseService` and `LibraryProvider` are byte-identical
across platforms.

So:

- A change to a screen, a service, a model, or a localization string belongs in
  **APP**, not here — even if the bug only shows in the browser. A web-only fix
  usually means editing the `_web` half of a conditional export *in APP*.
- A shim belongs here only if it wraps something outside the Flutter tree.
- `dist/` is generated. Never hand-edit it.
- Nothing here may point back at APP as a chain dependency. APP is the root of
  the graph; an edge into it makes propagation loop.

## Status

The chain wiring is in place; the build pipeline is **not set up yet**. Today
the web build comes straight from APP (`flutter build web --release`, and
`deploy-web.yml` there). The `app-source` handler in `tools/chain-propagate.mjs`
reports that and skips rather than writing an empty change.

## Mirrored files — do not edit here

`chain/chain.json`, `tools/chain-*.mjs`, and everything under `.claude/` are
**generated output**, mirrored from APP by `tools/mirror-claude.mjs`. Each
carries a header saying so. A local edit is lost on the next mirror.

To change one: edit it in `QuetzaLib-APP/.claude/` (or `chain/`, or `tools/`),
then run `node tools/mirror-claude.mjs` from APP.

Note that `tools/` here holds **both** this repo's own build scripts and the
mirrored `chain-*.mjs` files. Only the `chain-*.mjs` ones are generated.

## Useful commands

```bash
node tools/chain-lib.mjs      # this repo's upstream/downstream edges
node tools/chain-survey.mjs   # what moved in the other repos since last look
```

## Releases

This repo has no release train of its own — it deploys. APP ships `v*` and EXE
ships `exe-v*`.
