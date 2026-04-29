# ADR-011: Record retention policy (supersession over deletion for ADRs, handoffs, and retros)

**Status**: Accepted
**Date**: 2026-04-29
**Context**: `~/.windsurf/rules/document-as-you-go.md` codifies "supersession over deletion" — but only for ADRs. Sprint 1 review (`docs/reviews/sprint-1-review.md` §3.3 m-3) noted that the Sprint 1 entry handoff (`cascade-a-sprint-1.md`) is marked "superseded — Sprint 1 closed" in the handoffs INDEX but the file is retained — which is the right behavior, yet no rule formalizes that retention applies to handoffs too. The policy gap is minor now but grows with each vertical Cascade handoff authored, and the principle — "the reasoning trail must be durable; deletion erases the trail" — clearly applies to handoffs and retros as much as ADRs.

**Decision**: Extend "supersession over deletion" to cover all three record types — ADRs, handoffs, and retros. Specifically:

- **ADRs** — mark `Status: Superseded by ADR-MMM`; never delete. (existing policy)
- **Handoffs** — update `docs/handoffs/INDEX.md` status column (e.g., "superseded — <sprint> closed"); the file stays on disk.
- **Retros** — retros are historical records. Once `Status: closed`, they are immutable. They are never "superseded" because they snapshot a moment; a new retro adds to the record rather than replacing.

The rule amendment puts this in a single "Retention" section so all three cases share one authoritative prose.

**Alternatives considered**:

- **Per-record-type retention rules** — rejected; three rules for one principle is fragmentation.
- **Omit retros from the policy** — rejected; once closed, a retro's mutability would invite retrospective rewriting, which defeats the point of a retro.
- **Leave as convention** — status quo. Rejected per the review finding: convention without codification is an ADR-waiting-to-happen.

**Consequences**:

- Handoff files (and any future superseded records) accumulate; this is intentional — the INDEX keeps them discoverable and the INDEX status column communicates currency.
- `/docs-refresh` (M1.11) does not prune superseded records; it surfaces them as historical entries only.
- A future archive policy (e.g., "move superseded handoffs to `handoffs/archive/` after N sprints") would require its own ADR. Not adopted now — Sprint 1 demonstrated that retained-in-place is fine.

**Source**: Sprint 1 review report `docs/reviews/sprint-1-review.md` §3.3 m-3. Amendment lands in `~/.windsurf/rules/document-as-you-go.md` (new section "Retention") in the same hardening batch, alongside the ADR-numbering section (ADR-009) since both are record-lifecycle discipline.
