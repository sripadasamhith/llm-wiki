<!-- TEMPLATE -->
---
type: overview
title: Log
created: 2026-08-11
updated: 2026-08-11
tags: [meta]
status: active
confidence: high
sources: []
---

# Log

Append-only. Newest entries at the bottom. Never rewrite a past entry.

Every entry header must match `## [YYYY-MM-DD] <op> | <subject>` where `<op>` is one of
`ingest`, `query`, `lint`, `decision`, `capture`, so that the log stays greppable:

```
grep "^## \[" wiki/log.md | tail -20
```

---

## [2026-08-11] capture | wiki initialized

- Template scaffolded. No sources ingested yet.
