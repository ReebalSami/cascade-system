# ADR-004: Sprint 1 execution order

**Status**: Accepted
**Date**: Sprint 1 start

## Context

Sprint 1 (L1 Foundation) holds 11 milestones — issues #2 through #12 on GitHub Project #5. Issue numbering is a side-effect of creation order during Sprint 0 M0.6, not an architectural contract. Per `no-quantity-over-shape`, **dependencies decide order, not numbers**.

The plan (`~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 1) lists milestones M1.1 through M1.11 in a roughly logical grouping, but with two specific dependency tensions:

1. **M1.3 (phase-taxonomy contract) is the critical path** — explicitly called out in the plan: "Sprint 2 verticals B & C may spawn from M1.3 onward." The earlier M1.3 lands, the earlier parallel work can begin.
2. **M1.4 (`/start-project`) depends on M1.5 (`/run-phase`)** — M1.4's hand-off step (per its issue body, step 11) literally instructs: "call `/run-phase <first-phase>`". If M1.4 is authored before M1.5, that reference points to a non-existent file. M1.4's definition-of-done also includes an end-to-end dry run, which requires `/run-phase` to exist for full validation.

## Decision

Author Sprint 1 milestones in the following order (deviation from numerical order noted):

| Order | Milestone | Issue | Rationale |
|---|---|---|---|
| 1 | M1.3 | #4 | **Critical path.** Pure schema, no upstream deps. Unblocks Sprint 2. |
| 2 | M1.1 | #2 | Mechanical extraction; no deps. Establishes the rule-frontmatter precedent for `sources_consulted`. |
| 3 | M1.2 | #3 | First authored skill. Resolves plan §11 open question (exact frontmatter schema for SKILL.md). |
| 4 | **M1.5** | #6 | **Reordered.** Smaller dispatcher; only depends on M1.3. |
| 5 | **M1.4** | #5 | **Reordered.** Now references `/run-phase` as a real file in its hand-off step. |
| 6 | M1.6 | #7 | Artifact producers; consumes brainstorm output (M1.2). |
| 7 | M1.7 | #8 | Drift system; needs PRD concept (M1.6). |
| 8 | M1.8 | #9 | PM agent; consumes M1.6 issues + M1.7 labels. |
| 9 | M1.9 | #10 | Heartbeat skill; calls into M1.7, M1.8, and (forward-references) M1.10. |
| 10 | M1.10 | #11 | Meta skills; maintain everything else. Forward-reference from M1.9 is OK because skills are vendored docs, not runtime calls. |
| 11 | M1.11 | #12 | Documentation surface; closes Sprint 1. |

## Alternatives considered

- **Strict numerical order (M1.1 → … → M1.11)** — Rejected. M1.4-before-M1.5 introduces a forward-reference that fails the "literally runnable" test on M1.4's hand-off step. Numerical order is also not the plan's recommendation; the plan's prose explicitly hints at M1.3-first.
- **Mechanical-first (M1.1 → M1.3 → …)** — Considered. M1.1 is purely mechanical and has no deps, so it could go first. But M1.3 unblocks Sprint 2 verticals (parallelism gain), so M1.3 first dominates. M1.1 second is fine.
- **Bundle M1.4 + M1.5 as one batched milestone close** — Rejected by user during plan approval. Granularity in the running retro is preferred over throughput.
- **Author M1.10 (meta skills) before M1.9** — Considered to avoid M1.9 forward-referencing `/update-horizontal`. Rejected because skills are vendored docs (Markdown), not executable code with a build-time link check. M1.9's `SKILL.md` describing `call /update-horizontal` is authored once; first actual invocation happens during Sprint 2's `/sprint-review`, by which time M1.10 is done.

## Consequences

- **Per-milestone ritual** (fixed across all 11):
  1. Consult sources per `adapt-from-all`
  2. Author with `sources_consulted` + `adapted_for` frontmatter
  3. Real-time ADRs for any significant decision
  4. Close GitHub issue with link to artifact
  5. Append milestone outcome to `~/Projects/cascade-system/retros/sprint-1.md`
  6. If learning surfaced, append to `~/Projects/cascade-system/queue/pending-review.md`

- **Sprint 1 close ritual**: consolidate retro, stage L1-change-PR drafts as ADRs (since `/update-horizontal` lands in M1.10 and gets first real use during Sprint 2), author Sprint 2 handoff docs (`cascade-b-template-python-ml-uv.md`, `cascade-c-obsidian.md`).

- **Verticals can start spawning after milestone 1 closes** (M1.3 = critical path).

- **No per-milestone confirmation** — user explicitly authorized autonomous progression. Surface only on (a) plan-unresolved choice, (b) plan contradiction, (c) irrecoverable failure.

## Source

- `~/.windsurf/plans/sprint-1-execution-abf225.md` — execution plan reviewed and approved by user
- User's own reasoning during approval: "M1.4 literally ends with: 'call /run-phase <first-phase>' — that reference resolves to a real file if M1.5 is built first. … M1.4's 'dry-run end-to-end' definition of done literally requires M1.5 to exist for full validation. … Issue-number ordering has no architectural meaning; it's a side effect of how I created the issues. Per `no-quantity-over-shape`, dependencies decide order, not numbers."
- Plan: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 1
- GitHub issues #2–#12 on `ReebalSami/cascade-system`
