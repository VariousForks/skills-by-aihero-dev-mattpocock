# Replicating the plugin pattern for another skill — prompt template for AI agents

This file contains a copy-pasteable prompt you can give to a fresh Claude Code (or any other coding agent) session to apply the same pattern that was applied to `tdd/` to any other skill in this repo. The agent learns the pattern by reading the git commits — no out-of-band instructions needed beyond this prompt.

## How to use

1. Pick a target skill to convert. Examples: `write-a-prd`, `grill-me`, `prd-to-issues`, `triage-issue`, `improve-codebase-architecture`, `setup-pre-commit`, `git-guardrails-claude-code`, `write-a-skill`, `edit-article`, `design-an-interface`, `scaffold-exercises`, `request-refactor-plan`, `migrate-to-shoehorn`, `prd-to-plan`, `obsidian-vault`.
2. Open a fresh agent session inside this repo.
3. Paste the prompt below, replacing `<TARGET>` with the directory name of the skill you want to publish.

## The prompt

> You are working in the `aihero-dev/skills-by-mattpocock` repository. Your task is to make the `<TARGET>` skill installable as a standalone Claude Code plugin, following exactly the same pattern that was applied to `tdd/` in commits `68cef57..c6e5255` on the branch `feat/marketplace-with-tdd-as-installable-plugin` (or whichever branch this file lives on).
>
> Before doing anything:
>
> 1. Run `git log --reverse --format="%h %s%n%b%n----" feat/marketplace-with-tdd-as-installable-plugin -- tdd/.claude-plugin .claude-plugin/marketplace.json README.md CONTRIBUTING-PLUGINS.md AGENT-REPLICATION-PROMPT.md` (adjust branch name if needed) to read the full commit messages. They contain verbatim quotes from `code.claude.com/docs/en/plugins` and `…/plugin-marketplaces` justifying every layout decision. Do not skip this — the *why* is in those messages, and you will need it to make analogous decisions for `<TARGET>`.
> 2. Read `CONTRIBUTING-PLUGINS.md` — it is the human-readable recipe. The commits and the recipe agree by construction.
> 3. Read `<TARGET>/SKILL.md` and any companion `.md` files to understand the skill's content, internal cross-links, and any non-`.md` assets (e.g. a `scripts/` directory). Do not modify the content — only the layout and metadata.
>
> Then apply the pattern, one commit per logical step, mirroring the tdd commit sequence exactly:
>
> 1. **Manifest** — create `<TARGET>/.claude-plugin/plugin.json` with `name: "aihero-<TARGET>"`, a description copied verbatim from the entry in `README.md`, `version: "0.1.0"`, `author`, `homepage` (look for a blog post or video on aihero.dev for this skill; omit if none), `repository`, `license`. Commit with a message that includes the verbatim doc quote about manifest location and the namespace rule, in the same style as commit `68cef57`.
>
> 2. **Restructure** — `mkdir -p <TARGET>/skills/<TARGET>` then `git mv` `<TARGET>/SKILL.md` and every companion `.md` (and any non-script asset) into `<TARGET>/skills/<TARGET>/`. Verify internal sibling links like `[foo.md](foo.md)` still resolve — they will, as long as everything moves together. If `<TARGET>/scripts/` or any other non-skill asset exists, leave it at the plugin root (`<TARGET>/scripts/`), not under `skills/<TARGET>/`. Commit with a message in the style of commit `23be6c2`, citing the "Skills live in the `skills/` directory" doc quote and the "Common mistake: don't put skills/ inside `.claude-plugin/`" warning.
>
> 3. **Marketplace entry** — append a new object to the `plugins` array in `.claude-plugin/marketplace.json` with `name: "aihero-<TARGET>"`, `source: "./<TARGET>"`, `description`, `category`, `tags`, and `homepage`. Do NOT modify or remove any existing entries. Commit in the style of `2e9a52c`.
>
> 4. **README** — in `README.md`, find the bullet describing `<TARGET>` and append `Install: \`/plugin install aihero-<TARGET>@skills-by-mattpocock\`` to it. Do not restructure the README. Commit in the style of `4c779ff`.
>
> 5. **Verify** — run `claude --plugin-dir ./<TARGET>` in a scratch directory and confirm Claude lists `aihero-<TARGET>:<TARGET>` as an available skill. Then `/plugin marketplace add <repo-path>` followed by `/plugin install aihero-<TARGET>@skills-by-mattpocock` and confirm only `<TARGET>` (not other skills) is added. Document the verification output in the PR description, not in a commit.
>
> Style rules (match the tdd commits exactly):
>
> * Every commit message has a "Why this layout (verbatim from the official Claude Code docs)" section with quoted text and a `--` URL line.
> * Every commit message ends with `Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>` (or whichever model you are; match the trailer style of prior commits in this repo).
> * Use `git mv` for renames so history is preserved (`git log --follow` continues to work).
> * One PR per skill. Do NOT bundle multiple skills into one PR — Matt's review burden scales with the number of unrelated changes per PR, and the whole point of this opt-in structure is to let him say yes to one skill at a time.
> * Do NOT auto-publish other skills. The `plugins[]` array is an explicit allowlist.
>
> Final deliverable: a clean, rebased branch ready to PR, with a PR description that:
> * links to the original `tdd` PR as the precedent;
> * states "this PR converts `<TARGET>` only; no other skills are touched";
> * includes the verification output from step 5;
> * thanks Matt / AI Hero for the skill content.

## Why this prompt template lives in the repo

If a maintainer (or any contributor) wants to apply this pattern to more skills, the work is mostly mechanical — but the *justification* for each layout decision is non-obvious without the doc quotes. Putting the prompt in-tree means an agent (or person) can clone the repo, paste the prompt, and produce a parallel PR without ever leaving the repository to look up Claude Code's docs. The commits themselves are the spec; this file just tells an agent where to find that spec.

## Why feed agents commits, not just docs

Commits encode three things that raw docs do not:

1. **Concrete examples.** The tdd commits show what a "manifest" actually looks like for *this* repo's style — what fields, what tone, what `homepage` to use. The Claude Code docs show a generic example; the commits show the localized one.
2. **Local conventions.** Things like "the directory stays terse, the namespace gets the `aihero-` prefix" are not in the docs — they are emergent from this repo's prior naming style. Reading the rename commit (`c6e5255`) explains the convention.
3. **Decision rationale per choice.** Each commit message says *why* one option was picked over another (e.g. why `aihero-` not `mattpocock-`, why directory stays as `tdd` not renamed to `aihero-tdd`). An agent that follows the docs alone will have to re-derive these choices and may pick differently; an agent that reads the commits inherits the prior decisions for free.
