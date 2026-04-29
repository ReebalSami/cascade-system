# ADR-003: Strict enforcement of `docs/` placement rules

**Status**: Accepted
**Date**: Sprint 0 → Sprint 1 transition

## Context

I (Cascade A) created `handoff-cascade-a-sprint-1.md` directly in `docs/` root, violating the structure prescribed in plan §3.10 — and violating the `document-as-you-go` rule I had just authored in M0.5 by failing to write an ADR for the placement choice.

This is a recurrence-risk: there is no enforcement mechanism, only my own discipline. As the system grows and more skills/Cascades author docs, the entropy will increase unless rules are explicit and (eventually) automated.

## Decision

**All artifacts under `docs/` go in their canonical subdirectory.** Three exceptions only at root: `structure.md`, `makefile-manual.md`, `technical.md`.

**New subdirectory categories require an ADR before placement.** ADR-002 sets the precedent (handoffs → subdirectory + ADR-002).

**File-naming conventions are enforced**:

| Subdirectory | Pattern |
|---|---|
| `docs/decisions/` | `ADR-NNN-<kebab-slug>.md` (NNN zero-padded) |
| `docs/handoffs/` | `<cascade-id>-<sprint-or-vertical>.md` |
| `docs/retros/` | `sprint-N.md` (top-level meta-repo) or `<milestone-slug>.md` (downstream projects) |
| `docs/architecture/` | `*.excalidraw` or `*.mmd` |
| `docs/prompts/stages/` | `NN-<phase-slug>.md` |

**Migration of misplaced files happens immediately upon detection** — not later, not "as part of a refactor sprint." If you see a file in the wrong place, fix it now. ADR for the move if the destination requires a new category.

## Alternatives considered

- **Lax placement, refactor later** — Rejected. The retro for Sprint 0 already shows the cost of "later" thinking. Discipline is cheaper applied early.
- **Allow flat `docs/` for personal-style flexibility** — Rejected. The system explicitly aims to be transferable across projects (L3 templates), and a flat `docs/` doesn't transfer well.
- **Skip the rules entirely; trust /docs-refresh** — Rejected. /docs-refresh doesn't exist yet (M1.11). Even after it lands, post-hoc validation is worse than pre-hoc placement.

## Consequences

- `/docs-refresh` (M1.11) validates placement and naming. Until it lands, placement discipline is manual.
- Future Cascades reading `~/.windsurf/rules/document-as-you-go.md` will be reinforced by `~/Projects/cascade-system/docs/structure.md` (this ADR's companion).
- The handoff prompt template (`docs/handoffs/cascade-a-sprint-1.md`) explicitly references this ADR + structure.md so downstream Cascades inherit the rule.
- M1.1 (extract portfolio-website rules) should consider a new rule like `strict-docs-placement` derived from this ADR's intent — TBD if a separate rule is warranted vs subsuming under `document-as-you-go`.

## Source

- Self-review triggered by user feedback at Sprint 0 → Sprint 1 transition
- ADR-002 (the violation that motivated this meta-rule)
- Plan §3.10
