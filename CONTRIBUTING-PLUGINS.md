# Contributing: adding a skill to the marketplace

This repository ships its skills as individually-installable Claude Code plugins. Adding a skill to the marketplace is **opt-in, one skill per PR** — there is no automation that auto-publishes every top-level directory, and that is deliberate. This file is the recipe.

If you are an AI agent reading this to replicate the pattern that was applied to `tdd/`: every step below has a 1:1 mapping to a commit on the branch that introduced this file. Run `git log --reverse --format="%h %s" -- '.claude-plugin' 'tdd/.claude-plugin' 'tdd/skills' README.md CONTRIBUTING-PLUGINS.md` to see the canonical commit sequence.

## Why this layout (authoritative quotes)

The plugin layout below is not a convention this repo invented — it is what Claude Code's loader requires. The relevant verbatim quotes from the official docs (https://code.claude.com/docs/en/plugins and https://code.claude.com/docs/en/plugin-marketplaces):

* *"The manifest file at `.claude-plugin/plugin.json` defines your plugin's identity: its name, description, and version."*
* *"Skills live in the `skills/` directory. Each skill is a folder containing a `SKILL.md` file. The folder name becomes the skill name, prefixed with the plugin's namespace."*
* *"Don't put `commands/`, `agents/`, `skills/`, or `hooks/` inside the `.claude-plugin/` directory. Only `plugin.json` goes inside `.claude-plugin/`."*
* *"For plugins in the same repository, use a path starting with `./`. Paths resolve relative to the marketplace root, which is the directory containing `.claude-plugin/`."*

## Required final layout for a skill named `<name>`

```
<name>/
├── .claude-plugin/
│   └── plugin.json          # manifest: name, description, version, author
└── skills/
    └── <name>/
        ├── SKILL.md         # the skill itself (frontmatter + body)
        └── *.md             # any companion files referenced by SKILL.md
```

And one new entry in the marketplace catalog at the repo root:

```
.claude-plugin/marketplace.json
```

## Step-by-step recipe

### 1. Add the plugin manifest

Create `<dir>/.claude-plugin/plugin.json`:

```json
{
  "name": "aihero-<dir>",
  "description": "<one-line description, same wording as in README>",
  "version": "0.1.0",
  "author": { "name": "Matt Pocock", "url": "https://www.aihero.dev/" },
  "homepage": "<url to a blog post or video about this skill, if any>",
  "repository": "https://github.com/mattpocock/skills",
  "license": "MIT"
}
```

Two distinct names are at play here, **and they are decoupled on purpose**:

* `<dir>` — the on-disk directory name (e.g. `tdd`, `write-a-prd`, `grill-me`). Keep this terse and matching the existing repo convention. This is what `source: "./<dir>"` references in `marketplace.json`.
* `name` in `plugin.json` — the **plugin namespace** that becomes the install handle and the prefix for invocation (`/aihero-<dir>:<skill>`). Prefix with `aihero-` so the namespace conveys ownership when seen alongside plugins from other authors. The official Claude Code docs explicitly motivate namespacing this way: *"Plugin skills are always namespaced (like `/my-first-plugin:hello`) to prevent conflicts when multiple plugins have skills with the same name."* (https://code.claude.com/docs/en/plugins, Quickstart Step 4).

So for the existing TDD skill: `<dir>` = `tdd`, `name` = `aihero-tdd`, install handle = `aihero-tdd@skills-by-mattpocock`, invocation = `/aihero-tdd:tdd`.

Required: `name` (kebab-case). Everything else is optional but recommended.

### 2. Restructure the skill into `skills/<name>/`

```bash
mkdir -p <name>/skills/<name>
git mv <name>/SKILL.md <name>/skills/<name>/SKILL.md
# repeat for every companion .md file:
git mv <name>/<companion>.md <name>/skills/<name>/<companion>.md
```

Use `git mv` (not `cp` + `rm`) so rename history is preserved and `git log --follow` still works on the moved files.

If `SKILL.md` references siblings via relative links like `[tests.md](tests.md)` — those links stay valid as long as **all** companions move into the same new directory together. No link rewriting needed.

If the skill has a `scripts/` directory or any other non-skill assets, leave them at the plugin root (`<name>/scripts/`), not under `skills/<name>/`. The Claude Code `bin/` and `scripts/` conventions live at the plugin root.

### 3. Add the skill to the marketplace catalog

Edit `.claude-plugin/marketplace.json` at the repo root. Append one entry to the `plugins` array:

```json
{
  "name": "aihero-<dir>",
  "source": "./<dir>",
  "description": "<one-line description>",
  "category": "<development|planning|tooling|writing>",
  "tags": ["<tag1>", "<tag2>"],
  "homepage": "<url, if any>"
}
```

`source: "./<dir>"` resolves relative to the marketplace root (the directory containing `.claude-plugin/`), per the Claude Code docs. The `name` here **must match** the `name` field in `<dir>/.claude-plugin/plugin.json` — that's the install handle (`/plugin install aihero-<dir>@skills-by-mattpocock`) and the namespace prefix.

### 4. Update the README

In the section for the skill in `README.md`, you may add a "Install:" line referencing the per-skill install command:

```text
/plugin install aihero-<dir>@skills-by-mattpocock
```

### 5. Commit in small, reviewable steps

Suggested commit sequence (one commit per logical change, mirroring how `tdd` was added):

1. `feat(<dir>): add Claude Code plugin manifest at <dir>/.claude-plugin/plugin.json`
2. `refactor(<dir>): move SKILL.md and companions under <dir>/skills/<dir>/`
3. `feat: add aihero-<dir> entry to .claude-plugin/marketplace.json`
4. `docs(README): note aihero-<dir> as an individually-installable plugin`

Each commit message should include the doc quote justifying *why* the change is shaped that way, so future reviewers and AI agents can audit the rationale without leaving the repo. See the commits that introduced `tdd` for a template.

## Verification

Before opening a PR:

```bash
# 1. Direct loader path works
claude --plugin-dir ./<dir>

# 2. Local marketplace path works
/plugin marketplace add ./
/plugin install aihero-<dir>@skills-by-mattpocock
```

In each case, run the skill (or trigger it via its description) and confirm SKILL.md loads, companion-file links resolve, and any scripts execute.

## What NOT to do

* Don't auto-publish every top-level directory by globbing — keep `plugins[]` in `marketplace.json` an explicit allowlist.
* Don't put `skills/`, `agents/`, `hooks/`, or `commands/` inside `.claude-plugin/` — that's the documented "common mistake."
* Don't use `../` in `source` paths — relative paths must stay within the marketplace root.
* Don't rename a skill's namespace casually — the `name` field in `plugin.json` is public-facing and is how users invoke it.
