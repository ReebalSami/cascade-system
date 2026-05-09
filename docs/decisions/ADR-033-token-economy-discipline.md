# ADR-033: Token-economy discipline (selective Cascade D mitigations)

**Status**: Accepted
**Date**: 2026-05-08
**Plan**: `~/.windsurf/plans/sprint-review-cascade-d-mid-vertical-691e32.md` (Cascade D mid-vertical capture)
**Source retro**: `~/Projects/cascade-system/retros/cascade-d-mid-vertical-capture.md` §triage row "Session-level token-burn audit"
**Source queue entry**: `2026-05-08 — cascade-system — Cascade D — Session-level token-burn audit` (`queue/pending-review.md` line 260 pre-drain) — **Severity: High**
**Related**: `~/Projects/cascade-system/docs/rules/anti-laziness-core-principles.md`, `no-half-knowledge`, `no-quantity-over-shape`; ADR-018 (release-discipline cluster — `@release-manager` PR-close as natural session boundary); ADR-020 (`@kickoff` — fresh-Cascade pickup pattern already implicit)
**Issue**: tracked in retro; no separate GitHub issue (cross-cutting discipline)

## Context

The Cascade D session that ran M2D.0–M2D.4 (HORUS thesis bootstrap → identity → discipline → literature → brainstorm) consumed an estimated ~100k+ tokens in a single session. The user explicitly flagged this: *"tokens are burend extremly fast right now"*. The in-session audit (captured in `docs/handoffs/cascade-d-master-thesis.md` §5 and `queue/pending-review.md` line 260) identified five distinct drivers and proposed five mitigations.

**Driver breakdown** (per the audit):

| Driver | Estimated cost (M2D.0–M2D.4) |
|---|---|
| (a) Bulk-authoring 46 source stubs + 280-line synthesis at M2D.3 | ~40k tokens |
| (b) Full reads of `THESIS_BRAINSTORM_STATE_v2.md` (557 lines) in 2–3 chunks | ~15k tokens |
| (c) `@grill-me` walk-then-correct pattern at M2D.4 (Q1–Q5 multi-option asks before user course-correct) | ~13k tokens fully avoidable |
| (d) Cumulative context overhead (workspace rules + memories + AGENTS.md + prior turns) | ~5k tokens/turn |
| (e) Verbose response styling (multi-section headings, multi-bullet structures) | ~2–3k tokens/turn extra |

**Five proposed mitigations** (per the queue entry):

1. **Fresh-session-per-milestone** discipline
2. **Bulk-author batching** heuristic (CSV/YAML + `uv run` script over 30+ `write_to_file` calls)
3. **Partial-file-read** heuristic (default `grep_search` / `code_search` over `read_file` on long docs)
4. **Early course-correct** heuristic (stop after 1–2 pushback signals, ask "right shape?")
5. **Response-verbosity scaling** rule (tight for routine, structured for decision-gates)

**Triage from the mid-vertical capture review**: not all five are equally actionable. Some are concrete; others need empirical shape before promotion.

| Mitigation | Concreteness | Decision |
|---|---|---|
| 1 — Fresh-session-per-milestone | High — milestone close is a discrete event; `@release-manager` PR-close provides natural break | **ADOPT** |
| 2 — Bulk-author batching | Low — needs concrete CSV/YAML schema, `uv run` script template; one data point | **DEFER** to second-data-point trigger |
| 3 — Partial-file-read | Medium — `no-half-knowledge` already says "read fully when editing" but allows mapping via grep first; tension exists | **DEFER** — already implicit; promotion would require resolving the tension with `no-half-knowledge` |
| 4 — Early course-correct | Low — needs heuristic shape ("what counts as pushback?"); single data point (M2D.4 grill walk) | **DEFER** to second-data-point trigger |
| 5 — Response-verbosity scaling | Medium — concrete-enough principle; observable per-turn | **ADOPT** |

The two **ADOPT** items are coherent enough to land as a single discipline ADR. The three **DEFER** items remain as queue notes with re-evaluation triggers.

## Decision

**Adopt two coupled token-economy discipline practices**, codified as guidance (NOT a hard rule — these are heuristics, not contracts):

### Edit 1 — `cascade-system/docs/cheat-sheet.md` (or new section in `manual.md`)

Add a "Token-economy practices" subsection containing:

> **Fresh session per milestone**. For long-running verticals, open a new Cascade session per significant milestone rather than continuing past 50k+ accumulated tokens. The `@release-manager` PR-close event is the natural break point. Pickups go through `@kickoff` (vertical) or `/run-phase` (mid-vertical).
>
> **Response verbosity scales to stakes**. Tight responses for routine updates (milestone closures, validation-gate summaries, queue-entry captures). Structured responses (multi-section, multi-bullet) only for decision-gates, handoffs, retrospectives, and ADR drafts.

### Edit 2 — `~/Projects/cascade-system/docs/rules/<NEW>` (long-form archive only)

If, after a second observation of token-burn at M2E.x or future verticals, these practices need rule-status (always-on or `model_decision`-activated): author a `token-economy-practices` rule. **Not authored in this ADR** — Sprint 1 promotion discipline (`no-quantity-over-shape`) discourages premature rule-creation; one data point is insufficient.

For now: cheat-sheet line + manual narrative paragraph + this ADR as the canonical reference.

### Edit 3 — Three deferred mitigations stay in queue

`queue/pending-review.md` entry "Session-level token-burn audit" is **edited (not removed)** to reflect the partial drain:

- Original 5-item list → reduced to 3 deferred items (#2 bulk-author batching, #3 partial-file-read, #4 early course-correct)
- Add reference: *"Items #1 and #5 promoted to ADR-033."*
- Severity downgraded from **High** to **Medium** (the highest-risk items are addressed; remainder is optimization-tier).
- Re-evaluation trigger updated: *"After second long-running vertical (M2E.x or D-close) produces another token-burn data point."*

## Consequences

**Positive**:

- The two highest-leverage practices are codified without over-promotion.
- `@release-manager` PR-close already provides the natural fresh-session boundary — no new infrastructure needed.
- Verbosity scaling principle composes cleanly with existing `communication_style` directives ("be concise" / "minimize output tokens").
- Defers the under-shaped mitigations without dropping them — preserves them for empirical re-evaluation.

**Negative / risks**:

- Cheat-sheet entry is advisory-only — no enforcement. Mitigation: this is intentional; rule-promotion requires more data.
- Fresh-session discipline costs context-load on each new session (new `@kickoff` orientation pass). Mitigation: ADR-020 already establishes `@kickoff` as the lightweight pickup; the cost is bounded.
- The "50k tokens" threshold is a starting point, not a calibrated number. A future ADR may sharpen it.

**Neutral**:

- No new rule-file authored — no `global_rules.md` size pressure.
- No new skill or workflow — discipline is text-based reference.

## Alternatives considered

### Alt 1: Adopt all 5 mitigations as a single rule

Author a `token-economy-practices` rule covering all 5 items.

**Rejected** — `no-quantity-over-shape` and `no-half-knowledge` discipline. Items #2/#3/#4 lack concrete shape (one data point each); promoting them now would create rule-level claims that are still hypotheses. The cheat-sheet entry covers items #1 + #5 with appropriate "guidance not enforcement" framing.

### Alt 2: Drop items #2/#3/#4 entirely

Remove the deferred items from queue rather than keeping them.

**Rejected** — they remain valid hypotheses with concrete re-evaluation triggers (second token-burn data point). Per `bidirectional-learning-pipe`: defer ≠ drop.

### Alt 3: Author a heavier "session-management" skill

Build `@start-fresh-session` or `@compact-session` skills that automate the boundary discipline.

**Rejected** — single data point. Author a skill only when the discipline is both proven and sufficiently mechanical to script. Currently it's a human judgment call ("does this milestone warrant a fresh session?") that doesn't reduce well to automation. Re-evaluate at second data point.

## Apply via

`@update-horizontal` (per ADR-017). Edits are scoped to:

- `~/Projects/cascade-system/docs/cheat-sheet.md` — add "Token-economy practices" subsection
- `~/Projects/cascade-system/docs/manual.md` — add narrative paragraph (likely under existing "Sprint rhythm" or "Release discipline" section)
- `~/Projects/cascade-system/docs/decisions/INDEX.md` — flip ADR-033 row to `Status: Accepted` post-merge
- `~/Projects/cascade-system/queue/pending-review.md` — edit token-burn audit entry per "Edit 3" above

The cheat-sheet + manual edits are simple insertions. No L1 active-surface mutation (no skill / rule / workflow file edits). `@update-horizontal` step 8 Propagate table won't need a row — these are docs-only changes.

## Provenance

Sources consulted:

- `queue/pending-review.md:260` (M2D.0–M2D.4 token-burn audit, 2026-05-08)
- `~/Projects/horus/docs/handoffs/cascade-d-master-thesis.md` §5 (token-economics-note in HORUS handoff)
- `~/Projects/cascade-system/docs/rules/anti-laziness-core-principles.md` (production-quality discipline)
- `~/Projects/cascade-system/docs/rules/no-half-knowledge.md` (read fully — tension with mitigation #3 noted in DEFER decision)
- `~/Projects/cascade-system/docs/rules/no-quantity-over-shape.md` (don't promote until shape is clear — load-bearing for triage)
- ADR-018 (release-discipline cluster — provides the natural milestone-boundary event)
- ADR-020 (`@kickoff` — pickup pattern that complements fresh-session discipline)

Adapted for: cascade-system meta-repo conventions (cheat-sheet + manual as docs-only home for guidance; deferring rule-status until second data point).
