# Benchmark — local, practice set (kit)

Measured on this VM (shared 1.5 CPU, Node v24.22.1) with the kit's practice set
(3 cases: 257 / 348 / 486 files). `run total` is the time inside `run()` for all
3 cases, from the profiling harness (`/tmp/bench/profile.mjs`); the full process
(`node main.ts`) is ~0.5 s wall vs ~1.0 s for the house baseline.

| | run() total (3 cases) | median of 7 runs |
|---|---|---|
| makefun-site 1.0.0 (house baseline) | ~640–740 ms | ~700 ms |
| this entry | ~260–350 ms | ~307 ms |

Speedup on `run()`: **~2.3x** (median 701 / 307). Per case, typical:
g-0001 ~57 ms (baseline ~247 ms), g-0002 ~107 ms (~243 ms), g-0003 ~102 ms (~248 ms).

Correctness: all 3 practice cases byte-identical to the reference
(`practice/reference/expected.json`), plus 129 extra generated cases (30 seeds
through the kit's own generator) and 156 targeted edge cases (front matter,
Markdown, templates, slugs, links, statics) — 0 differences anywhere.

Where the time went in the baseline (profiled): Markdown 640 ms (75%),
layouts 166 ms (20%), front matter 26 ms. The wins here:
- Markdown inline rendering scans natively to the next construct and escapes
  plain runs in bulk (the reference escapes one character at a time).
- Blocks are classified by first character and rendered straight to the output
  array (no token objects, no per-line regex cascade).
- Templates compile once per case to flat bytecode and run on a small
  interpreter (no per-node tree walk).
- Slug/date caches per case, an ASCII fast path for slugify, cards computed
  once per source (the reference computes them twice per post), front matter
  scanned without splitting the body.

The final heat runs a fresh set from drand round 34249212, so these practice
numbers are a guide, not a promise — the same code paths run there.
