# ADR-035: `@release-manager` efficiency optimizations (selective)

**Status**: Proposed
**Date**: 2026-05-08
**Plan**: `~/.windsurf/plans/sprint-review-cascade-d-mid-vertical-691e32.md` (Cascade D mid-vertical capture)
**Source retro**: `~/Projects/cascade-system/retros/cascade-d-mid-vertical-capture.md` §triage row "`@release-manager` efficiency optimizations"
**Source queue entry**: `2026-05-08 — cascade-system — Cascade D — `@release-manager` efficiency optimizations` (`queue/pending-review.md` line 285 pre-drain) — **Severity: Low–Medium**
**Related**: ADR-018 (release-discipline cluster — defines the `@release-manager` skill + 4 helper workflows)
**Issue**: tracked in retro; no separate GitHub issue (small scope; partial close)

## Context

User feedback at M2D.4 closing exchange:

> *"with the release manager skill or workflow its working very good!! but its taking a lot of tokens. can we keep the quality with making it more efficient?"*

The Cascade D session ran `@release-manager` 5x during M2D.0–M2D.4 (one cold-start + four phase PRs) plus 2 small cascade-system meta-PRs (#93 handoff refresh + #94 queue note). Per-invocation cost: estimated ~5–8k tokens across the 4 helper workflows + orchestration + ledger summary. For very small PRs (1-line doc changes, queue notes), this overhead is disproportionate.

The queue entry proposed four sub-items. This ADR triages each:

| Sub-item | Decision |
|---|---|
| (a) `/ci-watch` no-CI short-circuit | **VERIFIED-IMPLEMENTED** (no edit needed) |
| (b) `--batch` micro-PR bundling | **DEFER** (needs design) |
| (c) Reduced ledger verbosity | **PARTIAL — guidance only, no mechanical edit yet** |
| (d) `--yolo` auto-approve | **REJECTED** (reduces auditability) |

## Decision

### Sub-item (a) — `/ci-watch` no-CI short-circuit: VERIFIED-IMPLEMENTED

Audit of `~/.codeium/windsurf/global_workflows/ci-watch.md` lines 24–41 confirms:

```
### 2 — Detect CI presence

gh pr checks <pr-number>

Exit codes / output:
- `0` with check rows → CI is configured; proceed to step 3
- `0` with empty output OR exit `8` (no checks) → no CI; report:
    "No CI checks configured for this PR.
     Merge gate is manual review only.
     ..."
  Exit clean (success — absence of CI is not a failure).
```

The short-circuit is in place. cascade-system + horus repos (no GitHub Actions) hit this path correctly — they don't loop on absent CI. **No edit required.** The queue entry's hypothesis ("may already be implemented") is correct.

This ADR documents the verification for posterity; future audits won't waste cycles re-checking.

### Sub-item (c) — Ledger verbosity: ADD GUIDANCE TO `@release-manager` SKILL.md, NO MECHANICAL EDIT YET

The current state is **already moderately compact** — `@release-manager` step 7 final report is 5 lines (SKILL.md lines 106–114). The audit-perceived verbosity is the **cumulative** output across:

- `/branch-start` report
- `/branch-push-and-pr` report (PR description + URL + status)
- `/ci-watch` report (initial state + delta lines + final state)
- `/branch-merge-and-cleanup` report (4-option choice + merge confirmation)
- `@release-manager` final orchestration report (the 5-line ledger)

For routine micro-PRs (queue notes, single-line doc changes), this stack is disproportionate. But mechanically suppressing intermediate reports risks losing audit trail — each helper report serves as durable evidence of what the workflow did at that step.

**Decision**: add a non-binding guidance section to `@release-manager` SKILL.md (under "Anti-patterns" or a new "Routine PR mode" subsection):

> **For routine PRs** (single-file docs, queue notes, single-line fixes): each helper workflow's report is acceptable but should be **delta-only** (no echoed inputs, no recap of unchanged state). The orchestrator's final report carries the complete ledger; intermediate reports are checkpoints, not transcripts.
>
> **For milestone-gated PRs** (PRs that close a `phases.yaml` milestone or land an L1 change): full intermediate reports are appropriate — auditability outweighs token cost.

This is **guidance, not a hard rule**. No flag, no mode switch — Cascade exercises judgment per-invocation based on PR shape.

**Why not author a `--routine` flag?** Single data point; the user's friction is real but not mechanical. A flag would shift the cost from "Cascade chooses verbosity" to "Cascade asks 'is this routine?' on every invocation" — replacing one overhead with another. Re-evaluate if a second data point makes the cost-benefit clearer.

### Sub-item (b) — `--batch` micro-PR bundling: DEFER

The proposal: allow accumulating 2–3 trivial changes onto a single branch and shipping as one PR at invocation-close.

**Why defer**: the design surface is non-trivial:

- How does the user signal "open the batch" vs. "close the batch and ship"?
- What if the batch grows mid-collection and warrants splitting?
- How does this interact with `phases.yaml` milestone-PR mapping (where each milestone wants its own PR)?
- Does it conflict with `/branch-start`'s "refuse to start a branch when already on a non-main branch" gate?

These are answerable but not on a single data point. Re-evaluate if the user (or future Cascade D / E sessions) repeatedly hits the "I want to bundle these 3 trivial changes" friction.

**Trigger for re-evaluation**: when a vertical produces ≥3 PRs of <50 lines diff each within a single milestone, OR when the user explicitly requests `--batch` again with concrete shape.

### Sub-item (d) — `--yolo` auto-approve: REJECTED

The proposal: opt-in `--yolo` flag or per-workflow allowlist that skips user-approval gates on `gh` + `git` commands deemed safe (e.g., `gh pr create --fill` after a verified-green push).

**Why reject**:

- `branch-and-pr-required` rule + ADR-018 explicitly establish PR description + merge approval as **load-bearing user gates**. The audit trail value comes from the user reviewing each gate, not from the gate's mechanical correctness.
- Solo-dev meta-repo has no second reviewer; the user's hard-gate review IS the review. Removing it removes the review entirely.
- Token savings from skipping approval prompts are marginal compared to the audit-trail cost.
- The user labelled this sub-item "controversial" in the queue entry — implicit acknowledgement that the trade-off is unfavourable.

**This rejection is final unless a fundamentally different proposal arises** (e.g., model-side automated PR review that replaces the user's review for trivial diffs — orthogonal capability not currently in scope).

## Consequences

**Positive**:

- Sub-item (a) is closed without effort — verified-existing capability documented.
- Sub-item (c) ships as guidance — no infrastructure change, behavioural improvement available immediately.
- Sub-items (b) and (d) get explicit dispositions — future audits don't re-litigate.

**Negative / risks**:

- Sub-item (c) guidance is non-enforceable — relies on Cascade's per-invocation judgment. Mitigation: judgment is what skills do; rules-not-mechanisms is the right tool when shape is unclear.
- The "routine vs. milestone-gated" distinction is itself a judgment call — Cascade may classify wrong. Mitigation: erring toward full reports is the safe failure mode (more verbose, never less; never loses audit trail).

**Neutral**:

- `--batch` deferral leaves a known friction point for repeat micro-PRs; the user can still drive batches manually (commit multiple changes to one branch before invoking `@release-manager`).

## Apply via

`@update-horizontal` (per ADR-017). Edits scoped to:

- `~/.codeium/windsurf/skills/release-manager/SKILL.md` — add "Routine PR mode" guidance subsection (under "Anti-patterns" or sibling)
- `~/Projects/cascade-system/docs/decisions/INDEX.md` — flip ADR-035 row to `Status: Accepted` post-merge

No edit to `/ci-watch` (sub-item a verified-implemented). No new flags / skills / workflows authored.

## Provenance

Sources consulted:

- `queue/pending-review.md:285` (release-manager efficiency queue entry, 2026-05-08)
- `~/.codeium/windsurf/global_workflows/ci-watch.md` lines 24–41 (verification of sub-item a)
- `~/.codeium/windsurf/skills/release-manager/SKILL.md` lines 106–114 (final-report compactness baseline)
- ADR-018 (release-discipline cluster — establishes the user-approval-gate-as-load-bearing principle, foundational for rejecting sub-item d)
- `~/Projects/cascade-system/docs/rules/no-quantity-over-shape.md` (load-bearing for deferring sub-item b without a second data point)

Adapted for: cascade-system meta-repo conventions (small-edit ADRs as valid scope per ADR-027/029 precedent), solo-dev review constraints (no second reviewer makes the user's hard gate non-negotiable).
