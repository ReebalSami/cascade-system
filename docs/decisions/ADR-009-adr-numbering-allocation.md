# ADR-009: ADR numbering allocation protocol (reserve-in-INDEX-first)

**Status**: Accepted
**Date**: 2026-04-29
**Context**: Sprint 1 review (`docs/reviews/sprint-1-review.md` §3.2 M-1) confirmed that two Cascades concurrently claimed `ADR-004` — neither had a mechanism to detect the other's claim. The new Cascade renumbered theirs to ADR-005 post-hoc with a `Note on numbering` line; the system survived but the episode revealed that `document-as-you-go` prescribed ADR authoring without specifying how to allocate NNN. With Sprint 2 spawning two parallel verticals (Cascades B + C) both authoring ADRs in the same `docs/decisions/` space, the probability of another collision is non-trivial.

**Decision**: Before creating `ADR-NNN-<slug>.md`, the authoring Cascade **first edits `docs/decisions/INDEX.md`** to reserve the NNN row — even as a stub. This claims the number atomically against the file-system. Only after the reservation lands does the Cascade author the ADR body. On a genuine race (both Cascades edit INDEX.md within the same window and the second overwrites the first), the losing Cascade renumbers post-hoc with a `Note on numbering` line at the top of their ADR and updates INDEX.md accordingly. Post-hoc renumber remains the fallback, not the default path.

**Alternatives considered**:

- **Stateless "next available N" computation at write time** — status quo. Rejected: that's exactly what failed in Sprint 1.
- **A dedicated `/write-adr` skill** — considered; adds ceremony for a simple protocol. The rule amendment is lighter and integrates with existing `document-as-you-go` practice. Revisit if the protocol proves inadequate.
- **Global ADR counter file** (e.g., `.adr-next-number`) — rejected; redundant with INDEX.md and another file to synchronize.
- **UUID-suffixed ADR filenames** — rejected; would break the human-scannable `ADR-NNN` pattern that makes cross-references legible.

**Consequences**:

- ADR authoring gains one pre-step (INDEX.md edit) — small ceremony cost.
- INDEX.md effectively doubles as a reservation ledger, which matches its existing role as the canonical catalog.
- Cross-Cascade collisions are rare by default; when they do occur, the renumber pattern is documented and low-drama (Sprint 1 demonstrated it works).
- `/docs-refresh` (which regenerates INDEX.md per M1.11) must be aware: it should not overwrite in-progress stub rows. Add a lint in a future tightening pass if needed.

**Source**: Sprint 1 review report `docs/reviews/sprint-1-review.md` §3.2 M-1 + retro `retros/sprint-1.md` line 39 observation ("ADRs are claim-by-write — no allocation gate"). Amendment lands in `~/.windsurf/rules/document-as-you-go.md` (new section "Numbering allocation") in the same hardening batch.
