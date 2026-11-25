# {repo} agent instructions

NEVER load `.env` on your own; ask first.

## What this is
{One paragraph.} Read `CONTEXT.md` for the terms, `docs/adr/` for the decisions and
`docs/plan.md` for where it's going.

## Commands
- `{install}`
- `{dev}`: {url}
- `{test}`, `{lint}`, `{typecheck}`

## Documenting
- Decisions, how they were built, and why changes were made live in `docs/` (read
  `docs/README.md`): `docs/adr/`, `docs/implementation/`, `docs/changes/`, each with a
  `_template.md`.
- A comment that would explain *why* points to its record instead (`// docs/changes/0001-....md`).
- A surprising or hard-to-reverse change comes with its record in the same commit.

## Committing
- One line: `<type>(<scope>): <short lowercase imperative summary>`.
- Stage only the files you changed, by path.

## Pull requests
Follow the `pr-process` skill before opening or updating a PR.

## Rules
- {The repo's own rules.}

## Glossary
Name concepts with their term in `CONTEXT.md`. A new or renamed concept updates it in the same commit.
