# A faster static site generator

[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/layerport/site-generator-speed-01/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/layerport/site-generator-speed-01/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)

Entry for the [Build a static site faster](https://make.fun/bounty/site-generator-speed-01-run-20261122) bounty on make.fun: a faster drop-in for makefun-site 1.0.0 that answers exactly as it does on every case, byte for byte.

## What it does

`solve.js` exports `run(input)`, which builds one site — `{ site, templates, files }` — into its output tree: every page's `index.html`, the index and tag pages, `build-report.txt` and each static file. `main.ts` is the harness driver: it reads `/input/cases.json`, answers each case with `run`, and writes `/output/answers.json`.

The implementation is written from scratch for speed:

- Markdown renders in a single pass straight from the line array — no token objects, no per-character escaping; plain runs are escaped in one chunk and a native regex jump finds the next inline construct.
- HTML escaping, slugifying and the word count are hand-rolled scans; slugs and long dates are memoised per case (titles and tag names repeat heavily inside one site).
- Front matter is read by index, and the body is sliced, never split and re-joined.
- Templates render through a small interpreter that writes into one shared output array and reuses its loop scope objects.

## How to run

```sh
node main.ts
```

It reads `/input/cases.json` (or `$MAKEFUN_INPUT_DIR/cases.json` outside a container) and writes `/output/answers.json` (or `$MAKEFUN_OUTPUT_DIR/answers.json`). Node 22, no dependencies, no network, fully deterministic — every run writes the exact same bytes.

## Local check

On the kit's practice set (3 cases) this entry passes 3/3 against the reference, with output byte-identical to makefun-site 1.0.0, at about 0.78 s CPU beside the baseline's about 1.31 s on this machine (median of 5 runs each).
