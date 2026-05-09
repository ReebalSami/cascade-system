# Cascade D mid-vertical capture — Retro

**Project**: cascade-system (horizontal Cascade A)
**Cascade**: A (horizontal — drain pass on behalf of Cascade D's in-flight observations)
**Milestone**: NOT a milestone close — explicitly mid-vertical capture covering Cascade D's M2D.0–M2D.4 session observations
**Status**: closed
**Date opened**: 2026-05-08
**Date closed**: 2026-05-08
**Plan ref**: `~/.windsurf/plans/sprint-review-cascade-d-mid-vertical-691e32.md`
**Source queue scope**: `queue/pending-review.md` lines 220–295 (7 entries from Cascade D's M2D.0–M2D.4 session, 2026-05-07/08)
**Issues closed in this milestone**: none (mid-vertical; no issue-close events)

> **Snapshot semantics**: this is a *mid-vertical* capture — Cascade D continues running. Observations recorded here are valid as of 2026-05-08. The vertical's own close retro (when M2D.x final phase ships) may revise, supersede, or extend any item below.

## What worked

- **Bidirectional learning pipe under load** — Cascade D captured 7 distinct observations across M2D.0–M2D.4 inline (per `bidirectional-learning-pipe` rule), without dropping any to "I'll write that down later" — every observation made it into `queue/pending-review.md` with structured `Insight + Source + Severity + Trigger`. Evidence: PR #93 + #94 + #95 each appended a queue entry corresponding to the friction surfaced in the same session. The discipline scales — it didn't degrade under M2D.0–M2D.4's high-pressure session.
- **`@release-manager` reliability** — 7 PRs over the M2D.0–M2D.4 window (5 vertical + 2 meta-repo), zero failures. User explicit feedback: *"working very good!!"*. The 4-helper-workflow split (`/branch-start` + `/branch-push-and-pr` + `/ci-watch` + `/branch-merge-and-cleanup`) is doing what ADR-018 intended — deterministic helpers, judgment in the orchestrator. No-CI short-circuit at `/ci-watch` step 2 is verified-implemented and prevents wasteful polling on cascade-system + horus repos.
- **Workspace-rule scope discipline holds** — Cascade D authored 2 project-local L2 rules (`horus-decision-discipline`, `horus-source-archival`) instead of pushing them to global. Right call per `no-quantity-over-shape` — single project's evidence is insufficient for L1 promotion. ADR-014 + ADR-018 + ADR-028 workspace-rule precedent is being applied correctly downstream.
- **`@kickoff` orientation pattern proves reusable across project types** — Cascade D's kickoff plan at `~/.windsurf/plans/kickoff-cascade-d-horus-362eef.md` followed the `@kickoff` SKILL.md procedure cleanly for a thesis-research vertical (different domain than C2's vault-write or B's L3-template work). The skill generalizes.

## Friction / surprises

- **Token economy under sustained authoring** — M2D.0–M2D.4 single session burned ~100k+ tokens. Surprised because the per-turn cost felt linear from inside the session, but the cumulative shape (M2D.3 bulk-authoring 46 source stubs ≈ 40k; full 557-line `THESIS_BRAINSTORM_STATE_v2.md` reads ≈ 15k; M2D.4 `@grill-me` walk-then-correct ≈ 13k avoidable) compounded faster than expected. Evidence: `queue/pending-review.md:260` audit + `~/Projects/horus/docs/handoffs/cascade-d-master-thesis.md` §5. **Why it surprised us**: previous verticals (B, C, C2) hadn't hit this pattern at this magnitude — first sustained-thesis-bootstrap run made the cost visible.
- **Per-session model selection is not ergonomic for phase-typed work** — Windsurf model selection is per-session via UI; no in-session switch. Cascade D ran in Opus 4.7 throughout, optimal for M2D.0–M2D.4's decision-heavy phases but suboptimal for M2D.5+'s code-heavy work. Evidence: user explicit ask in M2D.4 closing exchange. **Why it surprised us**: the friction was theoretical until the user articulated the optimal-model-per-phase-type pattern; previously the workflow assumed "one model for the whole session" was acceptable.
- **`/start-project` step 11 silently broken since shipped** — branch protection has been failing with HTTP 422 on every bootstrap. Evidence: M2D.0 HORUS bootstrap (2026-05-07) surfaced via debug. **Why it surprised us**: the failure was silent; `/start-project` reported success because the `gh api` call returned 422 but the workflow continued past the error. Server-side branch protection has been absent on every project bootstrapped via `/start-project` to date — relying entirely on the agent-side `branch-and-pr-required` rule. Surfaced as Severity Low–Medium because the rule has been load-bearing; defence-in-depth absent but no actual breach observed.
- **`@grill-me` walk-then-correct on imported brainstorms** — M2D.4 invoked `@grill-me` against the existing `THESIS_BRAINSTORM_STATE_v2.md` (557 lines, locked in v2 by user + Claude Opus 4.7 outside Cascade flow). The skill walked marker-by-marker as if it were authoring a fresh brainstorm; user course-corrected after 5 markers (~13k tokens) with *"don't lock anything; not even Research question; focus on what we need to start."* Evidence: `~/Projects/horus/docs/prompts/stages/02-brainstorm.md` §3 + chat trajectory. **Why it surprised us**: `@grill-me` and `@literature-review` (M2D.3, same session) both showed the same shape — built for fresh-authoring, not for import. Single-data-point at M2D.3 was repeat-observation at M2D.4.
- **Thesis vertical doesn't fit the python-ml-uv L3 template's brainstorm shape directly** — M2D.4 produced a 162-line directional artefact (`02-brainstorm.md`) shaped as discipline-commitments + implementation-start-blockers, not as a brainstorm walk. The L3 template's `phases.yaml` step `brainstorm` assumed `@grill-me` would author a marker-walked artifact; the thesis-import case needed a different shape. Evidence: M2D.4 deviation documented in the artefact's §1 Methodology. **Why it surprised us**: Vertical B's brainstorm shipped clean; the difference was Vertical B was greenfield (no pre-existing brainstorm), Vertical D had a v2 lock-in. The skill's procedure didn't anticipate the import case.

## Learnings

Each learning corresponds to a queue entry in scope (lines 220–295 of `queue/pending-review.md`). The disposition column carries the triage decision; PROMOTE entries become ADR drafts (this retro's §"ADRs written"), KEEP/DEFER/DROP edits land in `queue/pending-review.md` per `@sprint-review` step 10.

### Learning 1 — M2D.2 project-local tool-decision + source-archival discipline (KEEP)

- **Insight**: HORUS adopted 2 L2 workspace rules + 3 ADRs encoding tool-decision discipline + source archival per the user's meta-concerns. Single project's evidence is insufficient for L3 promotion to `python-ml-uv` template.
- **Source**: `queue/pending-review.md:220` (M2D.2 entry); `~/Projects/horus/.windsurf/rules/horus-{decision-discipline,source-archival}.md`; horus ADR-001 + ADR-002 + ADR-003.
- **Disposition**: **KEEP** in queue. Re-evaluation trigger refined to *"After Vertical D's `experiment` or `implement` phase produces first real tool-decision ADRs OR another `python-ml-uv` consumer hits the same need."*
- **Proposed L1 change**: deferred — see queue entry.

### Learning 2 — M2D.0 `/start-project` step 11 `gh api` schema bug (PROMOTE → ADR-032)

- **Insight**: Branch-protection invocation in `/start-project` step 11 fails HTTP 422 due to (1) string-typed `-f` values + (2) missing required schema fields. Workaround using `--input <tmpfile>` JSON-payload pattern proven at M2D.0.
- **Source**: `queue/pending-review.md:230` (M2D.0 entry); M2D.0 HORUS bootstrap session 2026-05-07.
- **Disposition**: **PROMOTE → ADR-032** (`Status: Proposed`). Mechanical workflow fix; awaits `@update-horizontal` apply.

### Learning 3 — M2D.3 `@literature-review --import-from` mode (KEEP)

- **Insight**: L3 `@literature-review` skill's procedure §1–§5 (focused-question scope verification → vault sweep → external sweep → triage interview → IMRAD per-source deep-read) doesn't fit the import case. Thesis-style consumer arriving with pre-existing review artefact needs different shape.
- **Source**: `queue/pending-review.md:239` (M2D.3 entry); HORUS `01-literature.md` §1 Methodology deviation.
- **Disposition**: **KEEP** in queue. Cross-reference Learning 4 (consolidates with `@grill-me --import-from`).
- **Proposed L1 change**: `--import-from <existing-doc-path>` flag — see queue entry. Trigger: second consumer hits same case.

### Learning 4 — M2D.4 `@grill-me --import-from` mode (KEEP)

- **Insight**: `@grill-me` walked v2 brainstorm marker-by-marker as if authoring fresh; ~13k tokens spent before user course-correct. Same shape friction as Learning 3.
- **Source**: `queue/pending-review.md:251` (M2D.4 entry); chat trajectory showing course-correct.
- **Disposition**: **KEEP** in queue. Cross-reference Learning 3. **Joint** L1 promotion candidate when either skill's import case fires for a second time.
- **Proposed L1 change**: symmetric `--import-from` pattern across phase skills (could become a shared L1 convention) — see queue entry.

### Learning 5 — Session-level token-burn audit (PROMOTE SELECTIVELY → ADR-033; 3 sub-items DEFER)

- **Insight**: 5-driver audit identified ~100k-token session cost. Two mitigations adoptable now (fresh-session-per-milestone + response-verbosity scaling); three need more shape (bulk-author batching, partial-file-read, early-course-correct).
- **Source**: `queue/pending-review.md:260` (Severity High); HORUS handoff §5.
- **Disposition**: **PROMOTE SELECTIVELY → ADR-033** (`Status: Proposed`) for the two adoptable mitigations as cheat-sheet + manual guidance. Three deferred items remain in queue with re-evaluation trigger *"After second long-running vertical produces another token-burn data point."* Severity downgraded High → Medium post-drain.

### Learning 6 — Model-switching advisory + handoff workflow (PROMOTE → ADR-034)

- **Insight**: User explicit ask for ergonomic phase-typed model switching (Opus for thinking, Sonnet for coding). Per-session model selection in Windsurf has no in-session switch; per-transition re-priming cost is significant.
- **Source**: `queue/pending-review.md:273` (Severity Medium); M2D.4 closing exchange (user verbatim).
- **Disposition**: **PROMOTE → ADR-034** (`Status: Proposed`). Three-artifact cluster: `model-selection-advisor` workspace rule + `@handoff-to-coding-session` / `@handoff-to-thinking-session` skill pair + cheat-sheet "When to use which model" entry.

### Learning 7 — `@release-manager` efficiency optimizations (PARTIAL → ADR-035)

- **Insight**: Per-invocation cost ~5–8k tokens; disproportionate for routine micro-PRs. Four sub-items proposed.
- **Source**: `queue/pending-review.md:285` (Severity Low–Medium); M2D.4 closing exchange.
- **Disposition**: **PARTIAL → ADR-035** (`Status: Proposed`). Sub-item (a) `/ci-watch` no-CI short-circuit verified-implemented (no edit needed); sub-item (c) ledger verbosity → guidance addition to `@release-manager` SKILL.md; sub-item (b) `--batch` deferred (needs design); sub-item (d) `--yolo` rejected (auditability).

## L1 change proposals (handed off to `@update-horizontal`)

| Proposal | L1 artifact affected | Status |
|---|---|---|
| Replace step 11 `-f` chain with `--input <tmpfile>` JSON-payload pattern (ADR-032) | `~/.codeium/windsurf/global_workflows/start-project.md` | proposed (ADR-032) |
| Add "Token-economy practices" subsection (fresh-session-per-milestone + response-verbosity scaling) (ADR-033) | `~/Projects/cascade-system/docs/cheat-sheet.md` + `~/Projects/cascade-system/docs/manual.md` | proposed (ADR-033) |
| Author `model-selection-advisor` workspace rule (ADR-034 artifact 1) | `~/Projects/cascade-system/docs/rules/model-selection-advisor.md` + INDEX.md | proposed (ADR-034) |
| Author `@handoff-to-coding-session` + `@handoff-to-thinking-session` skill pair (ADR-034 artifact 2) | `~/.codeium/windsurf/skills/handoff-to-{coding,thinking}-session/SKILL.md` | proposed (ADR-034) |
| Add "When to use which model" cheat-sheet entry (ADR-034 artifact 3) | `~/Projects/cascade-system/docs/cheat-sheet.md` | proposed (ADR-034) |
| Add "Routine PR mode" guidance to `@release-manager` SKILL.md (ADR-035 sub-item c) | `~/.codeium/windsurf/skills/release-manager/SKILL.md` | proposed (ADR-035) |

All six proposals route through `@update-horizontal` (per ADR-017 intake convention). ADR-034's skill-pair authoring routes via `@propose-extension` → `@write-skill` per artifact.

## ADRs written this milestone

| ID | Title | Trigger |
|---|---|---|
| ADR-032 | `/start-project` step 11 branch-protection `gh api` schema fix | M2D.0 HTTP 422 observed; mechanical workflow fix |
| ADR-033 | Token-economy discipline (selective Cascade D mitigations) | M2D.0–M2D.4 ~100k-token audit; user explicit concern |
| ADR-034 | Model-selection cluster (advisor rule + handoff skill pair + cheat-sheet entry) | M2D.4 closing exchange — user explicit ask for phase-typed model handoff ergonomics |
| ADR-035 | `@release-manager` efficiency optimizations (selective; partial close) | M2D.4 closing exchange — user feedback on per-invocation token cost; sub-item triage |

All four authored at `Status: Proposed`. Each cites `@update-horizontal` as the apply path. INDEX.md rows added with `Proposed` status; will flip to `Accepted` post-`@update-horizontal` apply.

## Carry-forward to next milestone / sprint

| Item | Why carry forward | Where tracked |
|---|---|---|
| **Cascade D vertical D continues** (M2D.5 onwards) | Mid-vertical capture; Vertical D's own close retro will land at HORUS milestone close. ADR-033 + ADR-034 may inform M2D.5+ session ergonomics. | HORUS handoff `~/Projects/horus/docs/handoffs/cascade-d-master-thesis.md` + this retro §"What worked" + §"Friction" |
| **Closed-vertical entries (B, C2, Sprint 1/2 holdovers) un-drained** | Out of scope per the user-selected mid-vertical scope. Will drain at next horizontal `@sprint-review`. | `queue/pending-review.md` (lines 16–217 unchanged; only Cascade D entries 220–295 drained in this retro) |
| **ADR-032 → ADR-035 await `@update-horizontal` apply** | Drafts at `Status: Proposed`; actual L1 mutation gated on `@update-horizontal` invocation by Cascade A in a future session. | `docs/decisions/INDEX.md` rows; `@update-horizontal` SKILL.md provides the apply path |
| **3 deferred token-economy mitigations** (bulk-author batching, partial-file-read, early course-correct) | Per ADR-033, kept in queue with explicit re-evaluation trigger. Single-data-point insufficient for promotion. | `queue/pending-review.md` (token-burn audit entry — to be edited per ADR-033 Edit 3) |
| **Joint `--import-from` flag** (Learnings 3 + 4 consolidated) | Two skills × one data point each = waiting on second consumer to fire either case. | `queue/pending-review.md` (M2D.3 + M2D.4 entries — both KEEP, cross-referenced) |

## Definition of done for this retro

- [x] All `## Friction / surprises` items have a learning (5 friction items map to learnings 5, 6, 2, 4, 4 respectively; learnings 1 + 3 + 7 stem from queue entries not separately surfaced as friction)
- [x] All `## Learnings` items either map to an L1 proposal or a project-local action (or are explicitly archived) — 4 PROMOTEs (2, 5, 6, 7), 3 KEEPs (1, 3, 4) with explicit re-evaluation triggers
- [x] L1 proposals are listed with their target artifact path (6 rows)
- [x] ADRs written this milestone are catalogued (4 rows: ADR-032 through ADR-035)
- [x] Carry-forward list is populated (5 items)
- [x] Status above changed to `closed` and `Date closed` filled in — gated on user-approved commit per ADR-008; user's *"proceed with the implementation"* directive (after plan approval) constitutes the approval; `@release-manager` PR description review provides the secondary hard-gate.

## Sign-off

**Closed**: 2026-05-08

**Clean-tree gate (ADR-008)**: not-yet-clean at retro write-time; staged files (2 modified + 5 new) are this PR's payload. Per ADR-008 escape-hatch: dirty state is intentional and routed via `@release-manager` for user-gated PR review. Working tree clean post-merge.

**Phase outcomes**:

- **Triage**: 7 Cascade D queue entries triaged, 4 PROMOTE → ADR drafts, 3 KEEP with re-evaluation triggers, 0 DROP.
- **ADR drafts staged**: ADR-032, ADR-033, ADR-034, ADR-035 — all `Status: Proposed`, all reserved in `INDEX.md`.
- **Queue housekeeping**: 7 entries edited (4 removed-replaced-by-ADR-refs, 2 KEEP-with-trigger-refinement, 1 KEEP-with-cross-reference, 1 DEFER-with-severity-downgrade). Queue header `Last drained` line to be updated.
- **No L1 active-surface mutation**: zero edits to `~/.codeium/windsurf/skills/`, `~/.codeium/windsurf/global_workflows/`, or `~/.codeium/windsurf/memories/global_rules.md`. All proposed mutations gated on `@update-horizontal` apply at next horizontal session.

**Follow-ups carried forward** (per §7 Carry-forward):

- Vertical D continues; M2D.5+ phase will leverage ADR-033 + ADR-034 if applied before then
- Closed-vertical entries (B, C2, Sprint 1/2 holdovers) await next horizontal drain
- ADR-032 → ADR-035 apply via `@update-horizontal` in a future session

**What this retro did NOT do** (worth naming so future reviews don't waste cycles re-examining):

- Did NOT close Vertical D's milestone (this is mid-vertical; D's close retro is separate)
- Did NOT drain Vertical B's queue entries (M2B.1–M2B.6) — out of user-selected scope
- Did NOT drain Vertical C2's queue entries (M_C2.x clarifications + MOC heuristic) — out of scope
- Did NOT drain Sprint 1/2 holdovers (M-2 storage, portfolio rules, GA-3, iCloud CLI, etc.) — out of scope
- Did NOT apply any L1 mutation — all changes gated on `@update-horizontal`

---

*Template source: `~/.windsurf/templates/_shared/retro-template.md`. Skill used: `@sprint-review` (mid-vertical capture mode — narrower scope than vertical-close or sprint-horizontal-drain). L1 proposals handed off to `@update-horizontal`.*
