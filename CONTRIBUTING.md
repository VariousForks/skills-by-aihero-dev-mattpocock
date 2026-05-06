# Contributing to this fork

> 🤝 Greg (human) and Claude (AI) maintain this fork together — with much respect for Matt Pocock's TDD work.

**Issues and pull requests are welcome here, independently of upstream.** You do not need to wait for [`mattpocock/skills`](https://github.com/mattpocock/skills) to accept anything before contributing to this fork. This fork has its own life: it serves as the place where Claude Code plugin packaging for these skills actually lives and works *today*, while the upstream considers (or doesn't) [the feature request in mattpocock/skills#138](https://github.com/mattpocock/skills/issues/138).

## What this fork is for

* **A working installation target.** Users can `/plugin marketplace add VariousForks/skills-by-aihero-dev-mattpocock` and install individual skills *now*, without waiting for upstream merge decisions.
* **A pattern demonstration.** Every commit message documents *why* a layout choice was made, with verbatim quotes from the official Claude Code docs. Contributions here become reference material for anyone (human or AI agent) wanting to package skills for Claude Code in other repos.
* **An AI-friendly contribution surface.** [`AGENT-REPLICATION-PROMPT.md`](./AGENT-REPLICATION-PROMPT.md) is a copy-pasteable prompt that walks an agent through producing parallel PRs to this fork. PRs authored by AI agents (with human review) are welcome and explicitly encouraged.

## What "good" looks like for issues and PRs

Three things, all of which the existing commit history demonstrates:

### 1. A real **"Why?"** in the description

Not "what changed" — `git diff` already shows that. Tell the reader *why* this change is the right shape. Examples from this fork's existing commits:

* Citing the official spec verbatim ("*Skills live in the `skills/` directory*" — https://code.claude.com/docs/en/plugins) so reviewers see the constraint, not just the conclusion.
* Explaining the alternatives considered and rejected ("*Why `aihero-` and not `mattpocock-`*: the brand on aihero.dev outlasts any single contributor…").
* Capturing the debugging trail ("*How this was caught: `gh repo view aihero-dev/...` returned 'Could not resolve to a Repository'…*") so future readers can replicate the verification.

A PR description that stops at "this fixes X" is not enough. A PR description that explains the constraint, the alternatives, and the rationale is.

### 2. Testing demonstrated with a **concrete example**

The model is [`TDD-SKILL-IN-ACTION.md`](./TDD-SKILL-IN-ACTION.md): a transcript-style record of the `aihero-tdd:tdd` skill being installed and used end-to-end on a minimal Go fixture. It shows the install commands, the prompt sent, the Skill invocation line, the actual RED/GREEN narration, and the final code.

For your contribution, the equivalent might be:

* A new skill packaging? Show a session where the skill loads via `/plugin marketplace add` and triggers correctly. Capture the inner-Claude transcript (or a synthesised one) the way `TDD-SKILL-IN-ACTION.md` does.
* A bug fix? Show the failure mode before, the fix, and the verified passing state after — with the exact commands a reviewer can re-run.
* A README or docs change? Link to the relevant doc URLs and quote the lines that justify the wording.

This is not a hard rule for trivial changes (typo fixes don't need transcripts). It is a strong norm for anything load-bearing.

### 3. Commit-message style that mirrors the existing history

Look at any commit on `main-gw` for the template. Each one has:

* A subject line prefixed with `🤝` and a conventional-commits-ish type (`feat:`, `refactor:`, `docs:`, `fix:`).
* A second-line warm disclosure: *"Greg (human) and Claude (AI) shipped this together — with much respect for Matt Pocock's TDD work."* Adapt to your own human/AI co-authorship if relevant. The point is transparency about authorship, not the exact wording.
* A body with a "Why?" section, ideally citing the spec or upstream context, and an explanation of any alternative-rejected.
* Trailing `Co-Authored-By:` for AI co-authors when relevant.

Smaller, focused commits over large bundled ones. The existing 11-commit log on `main-gw` is the working example: each commit is one logical change.

## Branch model

* `main-gw` — default branch, customised trunk. Open PRs against this branch unless your change is specifically intended for upstream.
* `main-upstream` — pristine reference to upstream's `main`. Don't commit here directly; it's for clean future syncs of upstream changes.
* `feat/*` — feature branches. The example branch `feat/marketplace-with-tdd-as-installable-plugin` is the upstream-targeted version of the work that lives on `main-gw`. If your contribution is *also* upstream-suitable, consider keeping a parallel `feat/...` branch with no fork-specific changes (no fork-banner README, no fork-specific marketplace name, no `TDD-SKILL-IN-ACTION.md`).

## Issues are welcome too

Open an issue if you:

* Want to package one of the unconverted skills (`write-a-prd`, `grill-me`, `prd-to-issues`, `triage-issue`, `improve-codebase-architecture`, `setup-pre-commit`, `git-guardrails-claude-code`, `write-a-skill`, `edit-article`, `design-an-interface`, `scaffold-exercises`, `request-refactor-plan`, `migrate-to-shoehorn`, `prd-to-plan`, `obsidian-vault`) and want to coordinate before doing the work.
* Have a question about the layout, the namespacing, the marketplace name, or any other decision visible in the commit history.
* Hit a bug or unexpected behaviour with `/plugin install aihero-tdd@variousforks-mattpocock-skills` (or whichever skill you tried).
* Have a feature idea — for the packaging, the docs, the agent-replication prompt, or the verification approach.
* Want to suggest a different namespace prefix, a different marketplace name, or any other naming change.

A good issue describes the situation, the expected behaviour, and the observed behaviour, with enough detail for a reviewer (human or AI) to reproduce. If you're not sure how to verify, link or attach the smallest possible reproduction.

## Specific contribution paths

| Want to… | Look at… |
|---|---|
| Package an additional skill from this repo as a plugin | [`CONTRIBUTING-PLUGINS.md`](./CONTRIBUTING-PLUGINS.md) for the step-by-step recipe |
| Have an AI agent do the packaging for you | [`AGENT-REPLICATION-PROMPT.md`](./AGENT-REPLICATION-PROMPT.md) — paste it into Claude Code with `<TARGET>` substituted |
| Verify a packaged skill actually works after install | [`TDD-SKILL-IN-ACTION.md`](./TDD-SKILL-IN-ACTION.md) for the verification template |
| Improve docs, README, or the recipe itself | Just open a PR against `main-gw` |
| Fix a bug | Same — PR against `main-gw`, ideally with a before/after reproduction |
| Propose changes that should ALSO go upstream | PR against `main-gw`, AND open a parallel branch suitable for [`mattpocock/skills`](https://github.com/mattpocock/skills) following [their contribution guidelines](https://github.com/mattpocock/skills/blob/main/CONTRIBUTING.md) (if any) |

## Trying a change locally before opening a PR

Recommended verification cycle:

```bash
# 1. Direct loader path (fastest iteration)
claude --plugin-dir ./<your-skill>

# 2. Local marketplace path (closest to what users will run)
/plugin marketplace add ./
/plugin install aihero-<your-skill>@variousforks-mattpocock-skills
/reload-plugins
```

For both paths, run a small task that should trigger the skill, and confirm the `Skill(...)` invocation appears. Capture the relevant excerpt for the PR description.

## Credits and acknowledgement

* All skill content (the SKILL.md files and companion guides) is the work of [Matt Pocock / AI Hero](https://www.aihero.dev/). When you contribute a new packaging, the skill content stays Matt's; you are adding metadata, not authoring the skill itself.
* This fork's packaging, layout, and contribution norms are the work of Greg ([@gwpl](https://github.com/gwpl)) and Claude (the AI). Co-authorship is disclosed transparently in commit messages and PR/issue bodies.
* When your contribution lands, the same disclosure norm applies: be clear about who (human / AI / both) wrote what. The 🤝 marker on commits is a convention, not a requirement, but the underlying transparency is.

Thank you for considering a contribution.
