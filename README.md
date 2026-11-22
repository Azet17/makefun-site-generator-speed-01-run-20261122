[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/Azet17/makefun-site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/Azet17/makefun-site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

# makefun-site, faster

A from-scratch, dependency-free reimplementation of makefun-site 1.0.0
for the make.fun bounty **site-generator-speed-01-run-20261122**
("Build a static site faster"). `solve.js` exports `run(input)`, which
returns exactly the output tree makefun-site returns for the same input:
every page's `index.html`, the index and tag pages, `build-report.txt`
and each static file, byte for byte.

## How it is faster

The reference is a clean plugin pipeline over token objects with a regex
at almost every step. This entry keeps the exact semantics and removes
the overhead:

- **Hand-rolled Markdown** — line classification by `charCodeAt` instead
  of regexes, and a fused lex+render pass that never builds token
  objects. Inline rendering scans plain runs and pushes them unescaped;
  only structural pieces (`<code>`, `<strong>`, `<em>`, links, escapes)
  pay for escaping.
- **Hand-rolled front matter** — manual `key: value` scanning with the
  same first-error order and the same error strings as the reference.
- **Chunked template rendering** — templates render into one shared
  output array instead of concatenating a string at every node, with the
  same scope-chain lookup, truthiness and include-depth rules.
- **Cached slugification and long dates** — titles, tag names, headings
  and dates repeat across a site; each is normalised once.
- **No per-character work** — `escapeHtml` is a fast-path test plus a
  single slice-building pass; word counting is one scan per body line.

`main.ts` is the harness driver: it reads `/input/cases.json`, answers
each case with `run`, and writes `/output/answers.json`. Node 22,
stdlib only.

## Layout

```
solve.js                the reimplementation (run + build pipeline)
main.ts                 harness driver
```

## License

MIT — see LICENSE. Copyright (c) 2026 Azet17.
