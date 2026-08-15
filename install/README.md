# install/

A personal Claude Code skill that installs this template into any repo.

Custom slash commands are now skills: a personal skill at `~/.claude/skills/<name>/SKILL.md`
becomes `/<name>` in **every** project on your machine. Install it once:

```bash
mkdir -p ~/.claude/skills
cp -R install/wiki-init ~/.claude/skills/wiki-init
```

Or straight from GitHub, without cloning the template first:

```bash
mkdir -p ~/.claude/skills/wiki-init && \
curl -fsSL https://raw.githubusercontent.com/sripadasamhith/llm-wiki/main/install/wiki-init/SKILL.md \
  -o ~/.claude/skills/wiki-init/SKILL.md
```

Then, in any repo:

```
/wiki-init          # installs into kb/
/wiki-init docs/kb  # installs somewhere else
/wiki-init root     # installs at the repo root (empty repos only)
```

It clones the template, copies the scaffold in, rewrites the paths in `AGENTS.md` to match
the target directory, appends a pointer to the repo's root `CLAUDE.md` (creating it if
absent), runs the first-run interview, and commits.

`disable-model-invocation: true` means it only runs when you type `/wiki-init` — Claude
won't decide to install a knowledge base on its own.

## Keeping it current

The copy in `~/.claude/skills/` is a snapshot. When you change `install/wiki-init/SKILL.md`
here, re-run the `cp` above to pick it up.

## Shell alternative

If you'd rather not go through Claude Code, the raw copy step is:

```bash
git clone --depth 1 https://github.com/sripadasamhith/llm-wiki.git /tmp/llm-wiki-tpl && \
mkdir -p kb && \
cp -R /tmp/llm-wiki-tpl/{AGENTS.md,CLAUDE.md,raw,wiki,templates,docs} kb/ && \
cp /tmp/llm-wiki-tpl/.gitignore kb/.gitignore && \
cp -R /tmp/llm-wiki-tpl/.obsidian kb/.obsidian && \
rm -rf /tmp/llm-wiki-tpl
```

You then have to fix the paths in `kb/AGENTS.md` and add the root pointer yourself, which
is most of what the skill is for.
