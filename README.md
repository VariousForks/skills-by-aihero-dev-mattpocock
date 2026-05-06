# Agent Skills — fork (Claude Code plugin packaging)

## ⚡ Quick install: pick any skill (or several)

Add the marketplace once, then install whichever skills you want — one at a time, no all-or-nothing:

```text
/plugin marketplace add VariousForks/skills-by-aihero-dev-mattpocock
/plugin install aihero-tdd@variousforks-mattpocock-skills
```

Then `/reload-plugins`. The skill is now invocable as `aihero-<name>:<name>` (e.g. `aihero-tdd:tdd`). The shorthand resolves to the fork's default branch (`main-gw`) automatically — no branch specifier needed. If you'd rather pin to an exact branch/tag, use the full-URL form: `/plugin marketplace add https://github.com/VariousForks/skills-by-aihero-dev-mattpocock.git#main-gw`.

**Available plugins** (each can be installed independently):

* `aihero-tdd@variousforks-mattpocock-skills` — test-driven development loop
* `aihero-write-a-prd@variousforks-mattpocock-skills` — PRD interview
* `aihero-prd-to-plan@variousforks-mattpocock-skills` — PRD → multi-phase plan
* `aihero-prd-to-issues@variousforks-mattpocock-skills` — PRD → GitHub issues
* `aihero-grill-me@variousforks-mattpocock-skills` — Socratic plan review
* `aihero-git-guardrails-claude-code@variousforks-mattpocock-skills` — block dangerous git
* `aihero-improve-codebase-architecture@variousforks-mattpocock-skills` — architecture review
* `aihero-triage-issue@variousforks-mattpocock-skills` — bug triage with TDD-fix plan

Each of the 8 plugins was packaged on its own `feat/marketplace-with-<skill>-as-installable-plugin` branch (one PR's worth of changes per skill, branched from `main-upstream`) and consolidated here on `main-gw`. **Independent issues and PRs against this fork are welcome** — see [CONTRIBUTING.md](./CONTRIBUTING.md) for the contribution culture (Why?-driven descriptions, example-based testing, AI co-authorship transparency). [CONTRIBUTING-PLUGINS.md](./CONTRIBUTING-PLUGINS.md) is the recipe for packaging another skill from this repo.

### What you get after install

Once `aihero-tdd:tdd` is loaded, asking Claude for TDD-driven work changes its behavior in three specific, observable ways:

* **Interview before code.** Claude pauses and asks for the public interface, the first behavior to drive, and a priority list — instead of jumping straight to implementation. This is the skill's anti-pattern guardrail against bulk-test-writing followed by bulk-implementation.
* **Strict vertical slicing.** One test → one minimal implementation → run → repeat. No multi-test bursts, no implementation that runs ahead of the test that justifies it.
* **Explicit RED / GREEN narration.** Claude literally announces `RED confirmed (…)` after the failing run and `GREEN. Next behavior: …` after the passing run, giving you a clean audit trail when reviewing the session.

A full reproducible verification — empty Go module + `Greet(name string) string` driven from zero through two red-green cycles — is captured in [**TDD-SKILL-IN-ACTION.md**](./TDD-SKILL-IN-ACTION.md). Useful both as a sanity check after you install (does the skill actually trigger?) and as a transcript-style example of what the loop looks like in practice.

If after install Claude *doesn't* enter the interview / RED-GREEN flow on a TDD prompt, the most common cause is that `/reload-plugins` wasn't run after install, or the prompt didn't surface the trigger words from the skill description (`tdd`, `red-green-refactor`, `test-first`, `integration tests`). Trying again with one of those words usually fixes it.

---


> **This is a fork of [mattpocock/skills](https://github.com/mattpocock/skills).** All skill content (the SKILL.md files and their companion guides) is Matt Pocock's / AI Hero's work. This fork adds *packaging only*: the `tdd` skill is wired up as a Claude Code plugin (`.claude-plugin/plugin.json`), and a marketplace catalog (`.claude-plugin/marketplace.json`) at the repo root makes selective installation possible. Upstream feature request and full PR description: [mattpocock/skills#138](https://github.com/mattpocock/skills/issues/138).
>
> The default branch here is `main-gw` (Greg's customised trunk). The pristine upstream `main` is preserved as `main-upstream` for clean future syncs / PRs.
>
> 🤝 Greg (human) and Claude (AI) co-built the packaging — with much respect for Matt's TDD work.

A collection of agent skills that extend capabilities across planning, development, and tooling.

## Planning & Design

These skills help you think through problems before writing code.

- **write-a-prd** — Create a PRD through an interactive interview, codebase exploration, and module design. Filed as a GitHub issue. Install: `/plugin install aihero-write-a-prd@variousforks-mattpocock-skills`
- **prd-to-plan** — Turn a PRD into a multi-phase implementation plan using tracer-bullet vertical slices, saved as a local Markdown file in `./plans/`. Install: `/plugin install aihero-prd-to-plan@variousforks-mattpocock-skills`
- **prd-to-issues** — Break a PRD into independently-grabbable GitHub issues using vertical slices. Install: `/plugin install aihero-prd-to-issues@variousforks-mattpocock-skills`
- **grill-me** — Get relentlessly interviewed about a plan or design until every branch of the decision tree is resolved. Install: `/plugin install aihero-grill-me@variousforks-mattpocock-skills`

## Development

These skills help you write, refactor, and fix code.

- **tdd** — Test-driven development with a red-green-refactor loop. Builds features or fixes bugs one vertical slice at a time. Install: `/plugin install aihero-tdd@variousforks-mattpocock-skills`
- **triage-issue** — Investigate a bug by exploring the codebase, identify the root cause, and file a GitHub issue with a TDD-based fix plan. Install: `/plugin install aihero-triage-issue@variousforks-mattpocock-skills`
- **improve-codebase-architecture** — Explore a codebase for architectural improvement opportunities, focusing on deepening shallow modules and improving testability. Install: `/plugin install aihero-improve-codebase-architecture@variousforks-mattpocock-skills`

## Tooling & Setup

- **setup-pre-commit** — Set up Husky pre-commit hooks with lint-staged, Prettier, type checking, and tests.
- **git-guardrails-claude-code** — Set up Claude Code hooks to block dangerous git commands (push, reset --hard, clean, etc.) before they execute. Install: `/plugin install aihero-git-guardrails-claude-code@variousforks-mattpocock-skills`

## Writing Skills

- **write-a-skill** — Create new skills with proper structure, progressive disclosure, and bundled resources.

## Installing individual skills as Claude Code plugins

Skills in this repository can be loaded by [Claude Code](https://docs.claude.com/en/docs/claude-code) as installable plugins, so you can opt in to one skill at a time without pulling everything else.

> **Note:** the plugin manifests live on this fork only. Until upstream [mattpocock/skills#138](https://github.com/mattpocock/skills/issues/138) is resolved, the install commands below point at this fork. If the upstream eventually adopts the same packaging, the same commands will work against `mattpocock/skills` directly.

Two ways to install **only `tdd`**:

**1. Direct, no marketplace** — clone the fork and point Claude Code at the skill folder:

```bash
git clone https://github.com/VariousForks/skills-by-aihero-dev-mattpocock
claude --plugin-dir ./skills-by-aihero-dev-mattpocock/tdd
```

**2. Via the marketplace** — adds the fork as a catalog and installs only the plugins you choose:

```text
/plugin marketplace add VariousForks/skills-by-aihero-dev-mattpocock
/plugin install aihero-tdd@variousforks-mattpocock-skills
```

After install, the skill is available as the model-invoked skill `aihero-tdd:tdd` (or invoked explicitly via the slash form `/aihero-tdd:tdd`).

The `aihero-` prefix is the plugin's namespace — it prevents collisions when a user has multiple TDD plugins from different authors installed at the same time. The directory on disk stays as `tdd/`; the namespace is purely a label set in `tdd/.claude-plugin/plugin.json`.

**Adding more skills to the marketplace** is opt-in. See [CONTRIBUTING-PLUGINS.md](./CONTRIBUTING-PLUGINS.md) for the per-skill recipe and [AGENT-REPLICATION-PROMPT.md](./AGENT-REPLICATION-PROMPT.md) for a copy-pasteable prompt that walks an AI agent through applying the same pattern to any of the unconverted skills above (`write-a-prd`, `grill-me`, `prd-to-issues`, etc.) — each new skill is one self-contained commit/PR.
