---
name: repo-docs
description: Use when setting up or tidying a Bilby repo's docs and agent instructions (AGENTS.md, CLAUDE.md, CONTEXT.md, docs/adr, docs/implementation, docs/changes), when writing an ADR, implementation record or change record, or when a code comment would explain why. The shared structure every Bilby repo follows.
---
# Repo docs

Every Bilby repo documents the same way, so anyone (or any agent) finds the why in the same place.
Example: bilbyai/auth (`docs/`, ADR 0015).

## The files
```
AGENTS.md        agent instructions: commands, rules, how we document (templates/AGENTS.md)
CLAUDE.md        one line, `@AGENTS.md`, so Claude Code reads the same file
CONTEXT.md       the glossary: the repo's terms, no implementation details
docs/
  README.md      how we document (templates/docs/README.md)
  plan.md        where the repo is going, short, linking its ADRs (optional)
  adr/           one per decision: what and why
  implementation/  one per decision once built: the parts, where, how tested
  changes/       one per change worth explaining: what changed and why
```
Each `docs/` folder has a `_template.md` (copy `templates/docs/`). Create folders when the first
record needs them.

## Rules
- **ADR** only when a decision is hard to reverse, surprising without context, and a real
  trade-off. Status `proposed`, `accepted`, `deprecated` or `superseded by NNNN`. Short: context,
  decision, consequences only if non-obvious.
- **Implementation record** when an ADR is built: what someone changing it needs to know.
- **Change record** for a change a reader would get wrong without the reason. Small, obvious
  changes need none.
- **Code comments point, they don't explain why**: `// docs/changes/0003-short-slug.md`. A comment
  saying what a tricky line does is fine.
- Numbering per folder, `0001-short-slug.md`, the next after the highest.
- A new or renamed term goes in `CONTEXT.md` in the same commit.
- To work a design out, use `grill-with-docs` (grilling, then domain-modeling) if it's installed:
  it writes the ADR and glossary as you go.

## Setting up a repo
1. Copy `templates/AGENTS.md` to the repo root and fill in its blanks (commands, rules); keep an
   existing AGENTS.md and add its "Documenting" section instead.
2. Add `CLAUDE.md` containing `@AGENTS.md`.
3. Copy `templates/docs/` to `docs/`. Add `CONTEXT.md` when the first term is settled.
