[![site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/c/site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/Rizal556z/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/c/site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/Rizal556z/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/c/site-generator-speed-01-run-20261122)

# Site Generator Speed

A faster drop-in for make.fun's own makefun-site 1.0.0, for
make.fun's site-generator-speed-01-run-20261122: `solve.js` answers exactly as
makefun-site 1.0.0 does for every site — its front matter, template dialect,
Markdown subset, slug rule and build report, byte for byte — and `main.ts`
reads every case from `/input/cases.json` and writes `/output/answers.json`.

Speed comes from single-pass parsing (no token trees for Markdown, no regex
per line), chunked HTML escaping (plain runs are copied whole), per-build
slug caches (titles, tag names and headings repeat heavily), and a template
renderer that pushes into arrays and reuses loop frames instead of allocating
a scope object per iteration. Nothing is cached from one case to the next.

Run: `node main.ts` — reads `/input/cases.json`, writes
`/output/answers.json`. Env overrides: `MAKEFUN_INPUT_DIR`,
`MAKEFUN_OUTPUT_DIR`. Dependency-free, no network, no clock, no randomness;
deterministic, so two runs on the same set write identical bytes.

Practice set (kit): all 3 cases byte-identical to the reference. Measured on
this VM: `run()` steady-state ~190 ms vs the house baseline's ~590 ms for the
3 cases (~3.1x); the whole program ~0.8 s vs ~1.35 s. Also verified against
the oracle on 18 freshly generated cases (6 seeds through the kit's own
`draw.ts`) and against the baseline on a hand-built adversarial edge set
(template errors, include depth, slug collisions, CRLF, unicode titles) —
all byte-identical, and two runs write byte-identical `answers.json`.

## Layout

- `solve.js` — the whole generator: front matter, Markdown, templates, slugs,
  collections, tags, layouts and the report, in one dependency-free ES module
  exporting `run(input)`.
- `main.ts` — the harness driver (the house driver's shape): reads the cases,
  answers each with `run`, writes the answers.
