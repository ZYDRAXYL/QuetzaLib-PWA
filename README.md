# QuetzaLib-PWA

The installable browser build of **QuetzaLib** — build tooling, browser shims,
and the published `dist/` output.

The app itself lives in [QuetzaLib-APP](https://github.com/ZYDRAXYL/QuetzaLib-APP).

## What this repo is for

QuetzaLib's Flutter tree already targets the web: `sqflite_common_ffi_web` gives
it the same SQLite API over IndexedDB, and image storage swaps to a
`webimg://`-backed blob store. This repo owns the build *around* that — the
release pipeline, any shims the browser target needs, and the deployed output.

```
tools/   build + deploy scripts
shim/    browser-only shims
dist/    built output (generated — never hand-edited)
```

**It does not contain a copy of the app.** PWA is a *build target* of APP's
`lib/`, not a fork of it. Copying Dart source in here means every later fix has
to be made twice.

## Status

The chain wiring is in place; the build pipeline is **not set up yet**. Today
the web build is produced from APP directly (`flutter build web --release`, and
`deploy-web.yml` in that repo). `node tools/chain-propagate.mjs` in APP reports
the `app-source` edge as unimplemented and skips it rather than writing an empty
change.

## The chain

This repo is part of QuetzaLib's six-repository architecture. `chain/chain.json`
is the contract; `tools/chain-*.mjs` and `.claude/` are mirrored from APP and
must not be edited here.

```bash
node tools/chain-lib.mjs      # this repo's place in the chain
node tools/chain-survey.mjs   # what moved in the other repos
```

Full write-up: [`chain/README.md` in QuetzaLib-APP](https://github.com/ZYDRAXYL/QuetzaLib-APP/blob/main/chain/README.md).
