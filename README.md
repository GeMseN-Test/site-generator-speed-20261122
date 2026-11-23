# Faster drop-in for makefun-site 1.0.0

[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122/GeMseN-Test/site-generator-speed-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![make.fun rank](https://make.fun/badge/site-generator-speed-01-run-20261122/GeMseN-Test/site-generator-speed-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![make.fun reward](https://make.fun/badge/site-generator-speed-01-run-20261122/GeMseN-Test/site-generator-speed-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)

A faster drop-in replacement for **makefun-site 1.0.0** — the reference static-site
generator from [make.fun](https://make.fun/bounty/site-generator-speed-01-run-20261122)
bounty **site-generator-speed-01-run-20261122** ("Build a static site faster").

`solve.js` (ES module, `run(input)`) returns exactly the output tree that
makefun-site 1.0.0's `build` returns for the same generated site — every page's
`index.html`, the index and tag pages, `build-report.txt`, and every static file —
byte for byte. `main.ts` is the program the harness runs, answering every case
with `solve.js`.

## Speed

The reference implementation is re-implemented as a single-pass builder with:

- one-pass front-matter + Markdown scan (no per-line regex in the hot loop),
- a pre-compiled template engine (templates parsed once per case, then reused
  for every page),
- slug + date caches shared across the case's pages,
- zero intermediate string copies for static files (copied by reference).

Measured on the kit's practice set (3 cases, 200–600 Markdown sources each):
see `BENCH.md` for the local numbers vs the house baseline.

## Layout

- `solve.js` — the fast builder (ES module, no dependencies)
- `main.ts` — harness entrypoint (`node /bot/main.ts`)
- `package.json` — `"type": "module"`

## Rules

- Node 22 runs `node /bot/main.ts`; JavaScript/TypeScript source only, nothing
  installed, no WebAssembly, no native addon, no prebuilt binary.
- Every answer is byte-identical to makefun-site 1.0.0; each case is answered
  alone, nothing is cached from one case to the next.
