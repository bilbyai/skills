# How we document

Three kinds of record. Write one when a reader would otherwise ask "why?";
small, obvious changes need none.

| Folder | One per | Says |
| --- | --- | --- |
| `adr/` | decision | what we decided and why (hard to reverse, surprising, a real trade-off) |
| `implementation/` | decision, once built | how it was built: the parts, where they live, how it's tested |
| `changes/` | change worth explaining | what changed and why, with links to its ADR and PR |

- Number each folder on its own (`0001-short-slug.md`), the next number after the highest.
- Copy the folder's `_template.md`.
- A code comment that would explain *why* points here instead:
  `// docs/changes/0001-unique-login-index.md`.
- Terms go in `../CONTEXT.md`, the glossary, in the same commit.
- Working out a design: the `grill-with-docs` skill (grilling, then domain-modeling) writes the
  ADR and glossary as you go.

Where the repo is going, if written: [plan.md](plan.md).
