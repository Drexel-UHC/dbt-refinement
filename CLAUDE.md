# dbt-col-lineage

<!-- TODO: Add a one-sentence description of what this project does -->

## Stack

| Tool | Purpose |
|------|---------|
| **Python** | Primary language |
| **dbt** | Data transformation framework — models, tests, macros |
| **SQL** | Model definitions and lineage queries |
| **GitHub CLI (`gh`)** | PRs, issues, repo operations |

<!-- TODO: Fill in actual versions and additional tools as the stack solidifies -->

## Rules

- Be concise — quick responses to iterate fast.
- Use Grep, Read, Glob directly — they're fast and parallel. Never spawn a subagent for simple file reads or single-file searches.
- For data/SQL changes, verify against sample dbt output before reporting complete.
- The published package name/entry point must stay stable — do not rename without coordinating downstream consumers.

## Never Do

- **Never commit or push** without explicit permission in the last few lines of conversation.
- **Never merge PRs.** Merging is always a human action.
- **Never use GitHub closing keywords** (`Closes`, `Fixes`, `Resolves`) in commits or PR descriptions. Use `Related to #N` or `See #N` instead.

## Skills

Custom skills live in `.claude/skills/`. Invoke with `/skill-name`.

| Skill | Description |
|-------|-------------|
| `/ship` | Ship code via PR — branching, committing, pushing, and creating/updating PRs |
| `/critical-code-reviewer` | Rigorous adversarial code review — security, slop, edge cases |
| `/grill-me` | Relentless design interview — stress-tests a plan by walking every branch of the decision tree |
| `/grill-with-docs` | Grilling session that sharpens terminology against the domain model and updates CONTEXT.md + ADRs inline |
| `/to-prd` | Synthesizes conversation + codebase into a PRD, written to `src_{title}.md` at repo root |
| `/to-issues` | Breaks a plan/PRD into vertical-slice issue proposals, written to `src_{title}.md` at repo root |
| `/tdd` | Test-driven development with red-green-refactor loop and TDD philosophy guides |
| `/improve-codebase-architecture` | Surfaces deepening opportunities — shallow modules to consolidate for testability and AI-navigability |

**Trust this file** — search the source if anything looks stale, and update this file when you find drift.
