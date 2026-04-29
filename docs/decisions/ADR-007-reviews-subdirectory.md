# ADR-007: `docs/reviews/` subdirectory for peer-Cascade review reports

**Status**: Accepted
**Date**: Sprint 1 close (review of Sprint 1 deliverables)

## Context

After Sprint 1 closed, Cascade A (this conversation, acting as the system's horizontal/mentor Cascade) conducted a focused review of the Sprint 1 Cascade A's deliverables (the parallel Cascade that executed M1.1 – M1.11). The review produced findings that don't fit any existing `docs/` subdirectory:

- Not a **retro** (`docs/retros/`) — retros are self-reflective by the executing Cascade. Reviews are peer assessments by a mentor / horizontal-system Cascade.
- Not an **ADR** (`docs/decisions/`) — review findings aren't decisions; they're observations + recommended actions.
- Not a **handoff** (`docs/handoffs/`) — handoffs are forward-looking instruction sets. Reviews are backward-looking assessments.
- Not a **prompt artifact** (`docs/prompts/stages/`) — those are project-phase artifacts of a downstream project.

This is a recurring need: every sprint close, every vertical's exit, the horizontal Cascade reviews the work. A canonical home is needed.

## Decision

Add `docs/reviews/` as a new subdirectory category under `docs/`.

**Naming convention**: `<sprint-or-vertical-slug>-review.md`

Examples:
- `sprint-1-review.md` — review of Sprint 1 deliverables
- `sprint-2-vertical-b-review.md` — review of Cascade B's `python-ml-uv` template authoring
- `sprint-2-vertical-c-review.md` — review of Cascade C's Obsidian integration

**Contents**:

- Summary of what was reviewed
- Concurrency-damage check (when relevant — i.e., when multiple Cascades touched overlapping artifacts)
- Reachability check (when relevant — i.e., when the reviewed Cascade had unusual workspace scope)
- Findings categorized by severity: Critical / Major / Minor / Strong
- Each finding: file path, observation, recommended fix
- Verdict: ready-to-commit / needs-fixes / blocked

**Authoring authority**: the horizontal Cascade A. Verticals (B/C/D) don't review themselves or each other — that's horizontal Cascade A's role per plan §3.2.

**Triggering events**:
- Sprint close (e.g., Sprint 1 → Sprint 2 transition)
- Vertical close (e.g., Cascade B finishes `python-ml-uv` template)
- Mid-sprint when a vertical reports completion or stalls
- User-requested ("review the work so far")

## Alternatives considered

- **Place review in `retros/`** — Rejected. Retros are self-authored by the executing Cascade for their own reflection. Reviews are externally authored (different perspective, different audience).
- **Place review in `decisions/` as an ADR** — Rejected. ADRs are decisions; reviews are observations leading to decisions. ADRs that result FROM a review go to `decisions/`.
- **No formal subdirectory; surface review in chat only** — Rejected. Reviews need archival permanence. A vertical's review may be referenced by future verticals to learn from past mistakes.
- **Combine with `handoffs/` since both are cross-Cascade communication** — Rejected. Handoffs are forward; reviews are backward. Different temporality, different purpose.

## Consequences

- `docs/structure.md` (cascade-system) gains a `reviews/` entry under the canonical tree.
- The downstream-project variant in `~/.windsurf/templates/_shared/scaffold/docs/structure.md` does **not** automatically get `reviews/` — downstream projects don't typically self-review the way the meta-repo does. If a downstream project develops the need (e.g., the thesis project wants peer reviews of its experiments), promote via `/update-horizontal` then.
- `/docs-refresh` (M1.11) needs to know about `reviews/` for placement validation. The validation rule: file in `docs/reviews/` must match `<sprint-or-vertical-slug>-review.md`.
- Future Cascade-A invocations (mentor mode) write to this subdirectory rather than improvising location.
- Concurrent-Cascade authoring conflicts (per ADR-004 / ADR-005 lessons) less likely here — only Cascade A authors reviews.

## Source

- This very review: `~/Projects/cascade-system/docs/reviews/sprint-1-review.md` (the first instance and the trigger for this ADR)
- Predecessors: ADR-002 (handoffs subdir), ADR-003 (strict placement)
- Plan §3.2 (Cascade roles): Cascade A is horizontal/mentor; verticals are scoped to their domain
