# ADR-004: `_shared/scaffold/` pattern for cross-cutting L3 template files

**Status**: Accepted
**Date**: Sprint 1 (mid-sprint plan amendment, applies to M1.4 design)

## Context

Plan §3.10 prescribes a universal `docs/` structure for all projects: `structure.md`, `decisions/`, `handoffs/` (added in ADR-002), `architecture/`, `retros/`, `prompts/stages/`.

Plan §4 Sprint 2 M2B.3 listed scaffold files for the `python-ml-uv` L3 template that **included** docs subdirectory `.gitkeep` files alongside type-specific scaffold (pyproject.toml, Makefile, src/, tests/, etc.).

This couples cross-cutting concerns (universal docs structure) with type-specific concerns (Python project layout). Two consequences:

1. **Drift risk**: when a new docs subdirectory is added (e.g., `docs/handoffs/` via ADR-002, mid-sprint), every L3 template's scaffold list must be updated separately. Easy to miss; we already drifted on this once.
2. **Duplication**: every future L3 template (nextjs-app, python-pipeline, rl-research, …) repeats the same docs scaffold lines.

## Decision

Introduce **`~/.windsurf/templates/_shared/scaffold/`** for cross-cutting scaffold files. `/start-project` (M1.4) applies it **first**, then layers the type-specific scaffold on top with last-wins semantics.

### Resulting layout

```
~/.windsurf/templates/
├── _shared/
│   └── scaffold/
│       ├── docs/structure.md
│       ├── docs/decisions/INDEX.md
│       ├── docs/decisions/.gitkeep
│       ├── docs/handoffs/INDEX.md
│       ├── docs/handoffs/.gitkeep
│       ├── docs/architecture/.gitkeep
│       ├── docs/retros/.gitkeep
│       ├── docs/prompts/stages/.gitkeep
│       └── .windsurf/rules/strict-docs-placement.md
├── python-ml-uv/
│   ├── phases.yaml
│   ├── scaffold/                     # type-specific ONLY
│   │   ├── pyproject.toml
│   │   ├── Makefile
│   │   ├── src/<pkg>/__init__.py
│   │   └── tests/__init__.py
│   ├── skills/
│   └── rules/
└── <future-types>/
    └── scaffold/                     # also type-specific only
```

### `/start-project` execution order

```sh
# 1. Universal scaffold (cross-cutting)
cp -R ~/.windsurf/templates/_shared/scaffold/* <new-repo>/

# 2. Type-specific scaffold (may override _shared/)
cp -R ~/.windsurf/templates/<type>/scaffold/* <new-repo>/
```

Last-wins on file collision. A type may legitimately override a shared file (e.g., `nextjs-app` overrides `Makefile` if `_shared/` ever ships one).

`/start-project`'s SKILL.md must document the override behavior explicitly so users aren't surprised.

## Alternatives considered

- **Each L3 template re-specifies docs/ scaffold** (status quo before this ADR) — rejected for drift + duplication
- **Hard-code docs/ creation in `/start-project` code** (no `_shared/` directory) — rejected because it makes the scaffold non-extensible; adding a docs subdir would require editing `/start-project` rather than dropping a file
- **Have `/start-project` invoke `/docs-refresh` after scaffold copy** — rejected because `/docs-refresh` (M1.11) is a validator/regenerator, not a creator; circular dependency at bootstrap time
- **Symlink shared files into each L3 template directory** — rejected for portability (Windows) and audit clarity (`git diff` becomes confusing)
- **Generate docs/ at runtime by code in `/start-project`** — rejected because file-based scaffolds are auditable and skill-author-friendly; code-generated structure is opaque

## Consequences

- **M1.4** (`/start-project`) implements the two-pass copy.
- **M1.4** is also responsible for **creating** `~/.windsurf/templates/_shared/scaffold/` initially (it doesn't exist yet — pre-creating it before /start-project lands risks orphaned bits if scope changes).
- **M2B.3** (python-ml-uv scaffold list) **shrinks** — docs entries move to `_shared/`; python-ml-uv scaffold carries only Python-specific files.
- **All future L3 templates** automatically inherit the latest universal docs structure.
- **Adding a new universal docs subdir** (e.g., a hypothetical `docs/glossary/`) requires editing exactly one place: `~/.windsurf/templates/_shared/scaffold/`. Then re-running `/start-project` for any new project picks it up. Existing projects need a separate migration step (TBD; possibly `/update-vertical`).
- **Override behavior** documented in /start-project's SKILL.md.
- **Plan §3.10, §4 M2B.3, §5 file-structure tree** all updated inline to reflect this pattern.
- **The `_shared/scaffold/.windsurf/rules/strict-docs-placement.md`** propagates ADR-003's enforcement rule to every downstream project, automatically.

## Source

- User feedback (Sprint 0 → Sprint 1 transition): *"did you also plan a skill for docs structure, when starting with verticals. so that also catched automatically when starting the vertical?"*
- Identified gap: ADR-002 added `docs/handoffs/` after M2B.3 was written, exposing the drift risk
- Predecessors: ADR-002 (`docs/handoffs/` subdirectory), ADR-003 (strict docs placement)
