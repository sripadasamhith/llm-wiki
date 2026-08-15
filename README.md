# llm-wiki

A repo template for a **project-specific LLM wiki**: a knowledge base that an LLM agent
builds and maintains for you, one source at a time, read through Obsidian.

Clone it at the start of a project. Point an agent at it. Feed it sources.

Based on [Andrej Karpathy's LLM wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f),
instantiated for Claude Code / Codex + Obsidian.

---

## The idea in one paragraph

RAG re-derives knowledge on every question. This doesn't. When you add a source, the agent
reads it, writes a summary, and then **propagates it through the wiki** — updating entity
pages, revising the synthesis, flagging where the new source contradicts an old one,
creating pages for concepts it introduced. The cross-references are already there when you
ask your question. The knowledge compounds instead of being rebuilt. You curate sources
and ask questions; the agent does all the bookkeeping, which is the part humans abandon.

---

## Structure

```
AGENTS.md      the schema — how the wiki is structured and how the agent must maintain it
CLAUDE.md      pointer to AGENTS.md, for Claude Code
raw/           immutable source documents; the agent reads, never writes
wiki/          everything the agent writes — index, log, overview, and the pages
templates/     page skeletons (source, entity, concept, question, decision)
docs/          Obsidian setup for you, the human
```

Three layers: **raw sources** (yours, immutable) → **the wiki** (the agent's, entirely) →
**the schema** (`AGENTS.md`, co-owned and co-evolved).

`AGENTS.md` is the important file. It's what makes the agent a disciplined wiki maintainer
rather than a chatbot that writes summaries. Read it before your first session and edit it
whenever you find a convention that works better for your domain.

---

## Using this as a template

**GitHub template repo** — push this repo, then Settings → check *Template repository*.
New projects: "Use this template" → new repo, done.

**Or just clone:**

```bash
git clone <this-repo> my-project-wiki && cd my-project-wiki
rm -rf .git && git init && git add -A && git commit -m "init wiki"
```

**Or use the `/wiki-init` skill** — installs the template into any repo in one command,
including path fixups and the root `CLAUDE.md` pointer. Set it up once:

```bash
mkdir -p ~/.claude/skills && cp -R install/wiki-init ~/.claude/skills/wiki-init
```

Then `/wiki-init` in any project. See [install/README.md](install/README.md).

**Or keep it as a subdirectory** of an existing project — a `wiki/` alongside your code —
by copying `AGENTS.md`, `CLAUDE.md`, `raw/`, `wiki/`, and `templates/` into it. For a
software project, this is often the better shape: the wiki lives next to the code it
describes and moves with it.

---

## First session

1. `git clone` the template, `cd` in, open the folder as an Obsidian vault
   (see [docs/obsidian-setup.md](docs/obsidian-setup.md) — 5 minutes).
2. Drop a first source into `raw/`.
3. Start your agent in the repo root and say:

   > Read AGENTS.md. This is a fresh wiki — run the first-run setup, then ingest the file
   > in raw/.

   It will interview you about the project, fill in the Project section of `AGENTS.md`,
   and walk the first ingest out loud so you can tune the workflow before it hardens.
4. Watch the pages appear in Obsidian. Push back on anything that doesn't fit — then have
   the agent write the correction into `AGENTS.md` so it sticks.

---

## Daily use

| You say | The agent does |
|---|---|
| "ingest `raw/thing.md`" | reads it, discusses takeaways, writes a source page, propagates to every affected page, updates the index, logs it |
| "how does X relate to Y?" | reads the index, drills into pages, answers with `[[citations]]`, offers to file the answer as a new page |
| "lint the wiki" | reports contradictions, orphans, dangling links, stale claims, and gaps — then waits for you to choose fixes |
| "remember that we decided X" | writes a decision page with rationale, indexes it, logs it |

Commit after each ingest. `git show --stat HEAD` then shows exactly which pages one source
moved.

---

## Choices this template makes

Karpathy's pattern is deliberately abstract; a template has to commit. What's fixed here,
and what to change if you disagree:

- **Wikilinks (`[[page]]`) over relative markdown links.** Better Obsidian ergonomics —
  autocomplete, rename-safety, graph view. Cost: links don't resolve when browsing on
  GitHub. If you read this more on GitHub than in Obsidian, flip `useMarkdownLinks` in
  `.obsidian/app.json` and update the link convention in `AGENTS.md`.
- **Markdown only, no scripts.** Nothing to install, nothing to maintain, works with any
  agent. If the wiki grows past a few hundred pages and `index.md` stops being enough
  routing, add a local search engine like [qmd](https://github.com/tobi/qmd) and tell the
  agent to use it.
- **YAML frontmatter on every page.** Costs a few lines per page, buys you Dataview
  dashboards over `status`, `confidence`, and `type` for free.
- **Contradictions are recorded, not resolved.** Newer doesn't automatically win. The
  disagreement between two sources is often the most interesting object in the wiki.
- **`AGENTS.md` is the schema, `CLAUDE.md` points at it.** Works with Claude Code, Codex,
  and anything else that reads an agent file, without maintaining two copies.
