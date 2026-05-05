# Agent Skills

A collection of agent skills that extend capabilities across planning, development, and tooling.

## Planning & Design

These skills help you think through problems before writing code.

- **write-a-prd** — Create a PRD through an interactive interview, codebase exploration, and module design. Filed as a GitHub issue.
- **prd-to-issues** — Break a PRD into independently-grabbable GitHub issues using vertical slices.
- **grill-me** — Get relentlessly interviewed about a plan or design until every branch of the decision tree is resolved.

## Development

These skills help you write, refactor, and fix code.

- **tdd** — Test-driven development with a red-green-refactor loop. Builds features or fixes bugs one vertical slice at a time.
- **triage-issue** — Investigate a bug by exploring the codebase, identify the root cause, and file a GitHub issue with a TDD-based fix plan.
- **improve-codebase-architecture** — Explore a codebase for architectural improvement opportunities, focusing on deepening shallow modules and improving testability.

## Tooling & Setup

- **setup-pre-commit** — Set up Husky pre-commit hooks with lint-staged, Prettier, type checking, and tests.
- **git-guardrails-claude-code** — Set up Claude Code hooks to block dangerous git commands (push, reset --hard, clean, etc.) before they execute.

## Writing Skills

- **write-a-skill** — Create new skills with proper structure, progressive disclosure, and bundled resources.

## Installing individual skills as Claude Code plugins

Skills in this repository can also be loaded by [Claude Code](https://docs.claude.com/en/docs/claude-code) as installable plugins, so you can opt in to one skill at a time without pulling everything else.

Two ways to install **only `tdd`**:

**1. Direct, no marketplace** — clone the repo and point Claude Code at the skill folder:

```bash
git clone https://github.com/aihero-dev/skills-by-mattpocock
claude --plugin-dir ./skills-by-mattpocock/tdd
```

**2. Via the marketplace** — adds the repo as a catalog and installs only the plugins you choose:

```text
/plugin marketplace add aihero-dev/skills-by-mattpocock
/plugin install tdd@skills-by-mattpocock
```

After install, the skill is available as the model-invoked skill `tdd` (or as `/tdd:tdd` if you prefer to invoke it explicitly).

**Adding more skills to the marketplace** is opt-in. See [CONTRIBUTING-PLUGINS.md](./CONTRIBUTING-PLUGINS.md) for the per-skill recipe — each new skill is one self-contained PR.
