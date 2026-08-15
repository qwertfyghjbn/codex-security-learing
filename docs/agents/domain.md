# Domain Docs

How agents should consume domain documentation in this repository.

## Current layout

This repository currently uses a single-context layout.

Expected structure:

```text
/
├── CONTEXT.md
├── docs/adr/
└── src/
```

At the moment, `CONTEXT.md` and `docs/adr/` do not yet exist. Their absence is not an error. Agents should proceed with the repo's existing documentation unless a task explicitly requires creating domain docs.

## Before exploring

If these files exist later, read them in this order:

1. `CONTEXT.md`
2. `docs/adr/` entries relevant to the area being changed
3. repo-local docs for the relevant subsystem

## Consumer rules

- Prefer the vocabulary defined in `CONTEXT.md` once it exists.
- If an ADR conflicts with a proposed change, surface the conflict explicitly.
- Do not silently invent alternate domain terminology when canonical terms exist.
- If the repo later evolves into a multi-context layout, replace this document and add `CONTEXT-MAP.md`.
