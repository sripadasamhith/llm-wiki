---
name: wiki-init
description: Install the llm-wiki knowledge-base template into the current repository, wire it up so agents discover it, and run first-run setup. Use when the user wants to add an LLM wiki, knowledge base, or kb/ directory to a project.
argument-hint: "[target-dir | root]"
disable-model-invocation: true
allowed-tools: Bash(git clone:*) Bash(git status:*) Bash(git rev-parse:*) Bash(cp:*) Bash(mkdir:*) Bash(rm:*) Bash(ls:*) Read Write Edit
---

# Install the llm-wiki template

Template source: `https://github.com/sripadasamhith/llm-wiki.git`

Argument `$1` is the target directory. Default `kb`. The literal value `root` means
install at the repository root (only sensible in an empty or near-empty repo).

## 1. Check before touching anything

- Confirm the working directory is a git repo (`git rev-parse --git-dir`). If not, ask
  whether to `git init` first.
- Confirm the working tree is clean (`git status --porcelain`). If dirty, stop and say so
  — the user should commit or stash first, so this install lands as one reviewable diff.
- If the target directory already exists and is non-empty, stop and ask.

## 2. Copy the template in

Clone shallow into a temp dir, copy, clean up. Never leave the template's `.git` behind.

Subdirectory install (the default):

```bash
git clone --depth 1 https://github.com/sripadasamhith/llm-wiki.git /tmp/llm-wiki-tpl
mkdir -p <TARGET>
cp -R /tmp/llm-wiki-tpl/{AGENTS.md,CLAUDE.md,raw,wiki,templates,docs} <TARGET>/
cp /tmp/llm-wiki-tpl/.gitignore <TARGET>/.gitignore
cp -R /tmp/llm-wiki-tpl/.obsidian <TARGET>/.obsidian
rm -rf /tmp/llm-wiki-tpl
```

For a root install, copy the same paths to `.` instead, and skip step 3.

## 3. Fix the paths

`<TARGET>/AGENTS.md` refers to `raw/`, `wiki/`, and `templates/` as if they sat at the repo
root. Read it and rewrite every such path to be relative to the repo root
(`<TARGET>/raw/`, `<TARGET>/wiki/`, ...) — including the directory-layout code block and
the `grep` example. Do the same in `<TARGET>/CLAUDE.md`.

Leave `<TARGET>/.obsidian/app.json` alone. Its paths are relative to the vault, and the
user opens `<TARGET>/` as the vault, not the repo root.

## 4. Make the wiki discoverable

A nested `CLAUDE.md` is only loaded once an agent is already reading files in that
directory — too late for a question asked at the repo root. So:

- If the repo has a root `CLAUDE.md`, append a short pointer section to it.
- If it doesn't, create one containing only the pointer.
- If the repo has a root `AGENTS.md`, append the same pointer there.

The pointer, adjusted for the real target directory:

```markdown
## Knowledge base

This project's knowledge base is in `<TARGET>/`. Read `<TARGET>/AGENTS.md` before
answering questions about why the system is the way it is, and follow it when asked to
ingest, query, or lint.
```

Keep it to those few lines. Do not inline the wiki schema into the root file — it is long
and should enter context only when it is relevant.

## 5. First-run setup

Read `<TARGET>/AGENTS.md` and run its `First run` section: interview the user about
subject, mode, and purpose; fill in the Project block; prune the domain profile they are
clearly not using; reset the `<!-- TEMPLATE -->` seed files; delete the `First run`
section itself.

Ask the questions in one batch, not one at a time.

## 6. Report

Commit as a single commit (`chore: add llm-wiki knowledge base`), then tell the user:

- what was created, with a one-line diffstat
- to open `<TARGET>/` as an Obsidian vault, pointing at `<TARGET>/docs/obsidian-setup.md`
- that the next step is dropping a first source into `<TARGET>/raw/` and asking for an
  ingest

Do not summarize the template's contents back to the user. They can read them.
