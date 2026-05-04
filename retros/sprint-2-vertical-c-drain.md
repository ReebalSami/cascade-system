# Sprint 2 — Vertical C drain — Cascade A horizontal retro

**Status**: in progress
**Sprint**: 2 — Cascade A horizontal pass following Vertical C close
**Cascade**: A (horizontal — owns L1 + queue drain)
**Pairs with**: `retros/sprint-2-vertical-c.md` (Vertical C's milestone-scope retro, `Status: closed`)
**Plan ref**: `~/.windsurf/plans/sprint-2-vertical-c-drain-86a684.md`
**Milestone reviewed**: GitHub `Sprint 2 Vertical C — Obsidian integration` (#2 — closed on PR #53 merge, 2026-05-04)
**Date opened**: 2026-05-04
**Date closed**: (filled at close — Phase 3 ADR-008 gate)

> **Snapshot semantics**: rows in §3 reflect queue state **at decision time**, not at retro-write time. Dispositions applied during this review create the new queue baseline. Subsequent reviews should treat these decisions as a closed audit trail, not a mutable list.

## 1 — Why this retro exists

Vertical C closed cleanly on 2026-05-04 (PR #53). Its retro at `retros/sprint-2-vertical-c.md` is `Status: closed` and covers the milestone-scope retrospective (what shipped, friction, sign-off). It explicitly handed seven queue entries to *"Cascade A's next horizontal `@sprint-review`"* — that is **this** review.

This retro therefore is:

- **Not** a re-retrospection of Vertical C (that's `sprint-2-vertical-c.md`'s job, already done).
- **Not** a Sprint 2 close retro (Vertical B is stale, Vertical D not started — Sprint 2 isn't closed).
- **Yes** the horizontal-Cascade-A queue-drain pass per `@sprint-review` skill steps 5 (triage), 9 (hand off to `@update-horizontal`), 10 (drain).

Scope per user choice (planning-phase ask): **full queue triage** — all ~14 queue entries plus 3 strategic items from `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` = 17 items.

## 2 — What worked (this drain pass)

Observations carried from running the drain itself:

- **`@sprint-review` SKILL.md was a clean executable spec.** The 11-step procedure plus archive-policy step 10 plus ADR-008 clean-tree gate at step 8 plus retroactive-ADR check at step 6 plus "single decision per item, don't bundle" at step 5 all combined to a procedure that didn't need re-interpretation. Confirms ADR-019 / ADR-020-style thick-skill investment pays off at exercise time.
- **Vertical C retro §6 + post-M2C.6 gap analysis pre-staged the triage decisions.** Cascade C's bundle-hint ordering (M2C.5 first → M2C.3 → M2C.4) and the gap-analysis Q3 ("just `@sprint-review`") meant Cascade A's role here was *execute the documented intent*, not *reinvent it*. The handoff-as-spec pattern held.
- **The queue-archive policy activated mechanically on first drop.** Entry #3's trigger was *"first Sprint 2 `@sprint-review` that drops an item"*; this review drops/archives 4 entries (#1.1, #1.2, #5, #7), so `queue/archive/` is created with `sprint-1-5.md` containing the two RESOLVED entries from Sprint 1 / 1.5.

## 3 — Triage decisions (the 17 items)

Per skill step 5 *"single decision per item, don't bundle"*. Each row applied during this review.

| # | Entry | Decision | Action |
|---|---|---|---|
| 1.1 | M1.1 / `skill-by-skill` (deferred portfolio rule) | **DROP** | Removed from queue. Rationale: no focus-loss friction observed across Verticals C or in any horizontal sprint (1.0, 1.5, 2/C). Speculative concern superseded by `no-half-knowledge` + `make-sure-it-works` already covering scope discipline. |
| 1.2 | M1.1 / `local-demo-before-push` | **DROP** | Removed from queue. Rationale: `make-sure-it-works` absorbs the principle; concrete UI-demo discipline is L3-template territory (already in portfolio-website L2). |
| 1.3 | M1.1 / `config-yaml-first` | **DEFER** | Stays in queue. Trigger: when first nextjs-app L3 template is authored. Re-evaluate at next horizontal review. |
| 1.4 | M1.1 / `github-project-management` | **DEFER** | Stays in queue. Trigger: `@sync-github` cross-repo exercise (not yet performed). Re-evaluate at next horizontal review. |
| 2 | M-2 / L1 storage strategy | **DEFER** | Stays in queue. Big architectural decision (4 candidate options); needs dedicated `@grill-me` session. Recommended pairing: Sprint 3 strategic vertical alongside GA-1 write-path. Not blocking Sprint 2 / B / D. |
| 3 | Queue archive policy | **PROMOTE-NOW (mechanical)** | Resolved by THIS review. Created `queue/archive/.gitkeep` + `queue/archive/sprint-1-5.md` with archived entries. Removed from queue with a back-pointer note. |
| 4 | Brainstorm drift correction (ADR-018) | **DEFER** | Stays in queue. No second dispatch-time correction surfaced; trigger remains *"next dispatch-time correction in another build"*. |
| 5 | Release-discipline cluster gap (RESOLVED by PR #14/#18) | **ARCHIVE** | Moved to `queue/archive/sprint-1-5.md`. Marker text already announced this on archive-policy activation. |
| 6 | ADR-006 path drift / `@verify-l1` enhancement | **PROMOTE** | Hand-off to `@update-horizontal` → `@verify-l1`. Phase 2.6, ADR-029. |
| 7 | Vertical-spawn bootstrap (RESOLVED by ADR-019+ADR-020) | **ARCHIVE** | Moved to `queue/archive/sprint-1-5.md`. Same marker pattern. |
| 8 | M2C.3 / `@grill-me` vault context | **PROMOTE** | Hand-off to `@update-horizontal` → `@grill-me`. Phase 2.2, ADR-025. |
| 9 | M2C.3 / `@to-prd` vault sources | **PROMOTE** | Hand-off to `@update-horizontal` → `@to-prd`. Phase 2.3, ADR-026. |
| 10 | M2C.3 / `/recalibrate` 7th drift signal | **PROMOTE** | Hand-off to `@update-horizontal` → `/recalibrate`. Phase 2.4, ADR-027. |
| 11 | M2C.4 / `obsidian-context-priming` rule (NEW) | **PROMOTE** | Hand-off to `@update-horizontal` (direct authoring path for new rules). Phase 2.5, ADR-028. |
| 12 | M2C.5 / `@vault-research` skill (NEW) | **PROMOTE-FIRST** | Hand-off to `@write-skill` (new skills route through `@write-skill` per `@update-horizontal` step 1). Phase 2.1, ADR-024. Bundle leader. |
| 13 | `@kickoff` milestone-mode candidate | **DEFER** | Trigger: *"After Vertical D closes — three verticals' worth of milestone-plan-creation behavior"*. B not done; D not started. Stays in queue. |
| 14 | Design-locked-plan size threshold | **DEFER** | Trigger: *"After Verticals B and D produce one or more design-locked plans"*. Insufficient data points. Stays in queue. |
| GA-1 | WRITE path to `wiki/` (`@vault-distill` candidate) | **CAPTURE + DEFER** | Appended as new queue entry. Severity high. Trigger: dedicated write-path Sprint 3 vertical. Pairs with item #2 (L1 storage). |
| GA-2 | ADR ↔ vault relationship (ADR-024 candidate) | **CAPTURE + DEFER** | Appended as new queue entry. Posture A (separation, current default) is fine until vault has `wiki/` content. Trigger: when `wiki/` accumulates ≥5 cards OR a future ADR struggles to reach the user via `docs/decisions/` only. |
| GA-3 | Session-hooks equivalent (Cole Medin pattern) | **CAPTURE + DEFER** | Appended as new queue entry. Blocked by Windsurf platform (no `SessionStart`/`PreCompact`/`SessionEnd` hooks); manual `bidirectional-learning-pipe` is the closest analog. Low priority; trigger: when Windsurf ships session callbacks OR `bidirectional-learning-pipe` shows manual-capture failure rate. |

**Tally**: 6 PROMOTE (5 substantive L1 promotions + 1 mechanical archive-policy activation) + 2 DROP + 8 DEFER (incl. 3 GA captures) + 2 ARCHIVE = 17 of 17.

## 4 — L1 change proposals (handed off to `@update-horizontal`)

| Order | Skill | Target | ADR | PR | Status |
|---|---|---|---|---|---|
| 1 | `@write-skill` | NEW `@vault-research` skill (M2C.5) | ADR-024 | (Phase 2.1) | proposed |
| 2 | `@update-horizontal` | `@grill-me` (M2C.3) | ADR-025 | (Phase 2.2) | proposed |
| 3 | `@update-horizontal` | `@to-prd` (M2C.3) | ADR-026 | (Phase 2.3) | proposed |
| 4 | `@update-horizontal` | `/recalibrate` (M2C.3) | ADR-027 | (Phase 2.4) | proposed |
| 5 | `@update-horizontal` | NEW `obsidian-context-priming` rule (M2C.4) | ADR-028 | (Phase 2.5) | proposed |
| 6 | `@update-horizontal` | `@verify-l1` (path drift sweep) | ADR-029 | (Phase 2.6) | proposed |

ADR numbers reserved by editing `INDEX.md` first (per ADR-009) at the start of each promotion. Each PR routes through `@release-manager`. **Bundle-anti-pattern note**: per `@update-horizontal` SKILL.md *"one ADR per logical change; bundling unrelated edits into one ADR loses traceability"*, these six are sequential, not bundled — even though Vertical C retro §6 hinted at bundling. The skill anti-pattern wins.

## 5 — ADRs written this milestone (Cascade A horizontal pass)

ADRs **emerging from this drain** (Phase 2 will land them):

| ID | Title | Trigger |
|---|---|---|
| ADR-024 | `@vault-research` auxiliary skill (M2C.5 promotion) | M2C.5 queue entry |
| ADR-025 | `@grill-me` vault context load (M2C.3 promotion) | M2C.3 queue entry |
| ADR-026 | `@to-prd` vault sources integration (M2C.3 promotion) | M2C.3 queue entry |
| ADR-027 | `/recalibrate` 7th drift signal (M2C.3 promotion) | M2C.3 queue entry |
| ADR-028 | `obsidian-context-priming` rule (M2C.4 promotion) | M2C.4 queue entry |
| ADR-029 | `@verify-l1` path-drift sweep (Sprint 1.5.6 promotion) | Sprint 1.5.6 ADR-006 finding |

ADRs **inherited** from the Vertical C milestone (catalogued for completeness — already landed in Vertical C PRs):

| ID | Title | Landed in |
|---|---|---|
| ADR-022 | Obsidian CLI selection over MCP architectures | PR #47 (M2C.1) |
| ADR-023 | Vault layout v3 + iCloud-symlink ratification | PR #49 (M2C.2) |

## 6 — Retroactive-ADR check (skill step 6)

Scanned for decisions made during Vertical C or in this drain that lack ADR coverage:

- **Queue-archive policy activation** (this review): no ADR needed; the policy was already documented in queue entry #3 with explicit trigger conditions. Activation is mechanical, not a new architectural decision.
- **Vertical C bundle-anti-pattern reconciliation** (Vertical C retro §6 said "bundle"; `@update-horizontal` SKILL.md says "don't bundle"; this drain chose don't-bundle): **decision is grounded in existing `@update-horizontal` ADR**, no new ADR. Captured as a process note in §4 above.
- **Cascade A horizontal pass naming convention** (this retro file is `sprint-2-vertical-c-drain.md` rather than `sprint-2.md`, since Sprint 2 isn't closed): no ADR needed — naming follows the existing pattern (`sprint-1-5.md` for an interstitial pass; this is a vertical-close-paired pass).

No retroactive ADRs required.

## 7 — Carry-forward (open items surviving this review)

| Item | Why carry forward | Where tracked |
|---|---|---|
| **Vertical B (`python-ml-uv` L3 template)** stale | Handoff exists (`docs/handoffs/cascade-b-template-python-ml-uv.md`) since Sprint 1 close; **zero issues filed; zero milestone created**. Cascade A action: `@kickoff` the handoff, OR run `/recalibrate` to surface the drift formally, OR explicitly archive Vertical B if it's been superseded. **Recommended: `@kickoff cascade-system/docs/handoffs/cascade-b-template-python-ml-uv.md`** in a fresh Cascade window when Sprint 2 work resumes on B. | This retro §7 + `@kickoff` invocation pending |
| **Vertical D (thesis dogfood)** not started | Per `cascade-project-system-cac5f9.md`: D consumes B's `python-ml-uv` template + benefits from C's vault layer. D is Sprint 3, not Sprint 2. No handoff yet; Cascade A will author when B closes. | Sprint 3 planning |
| **8 deferred queue entries** | Items #1.3, #1.4, #2, #4, #13, #14 + GA-1, GA-2, GA-3 stay in queue with explicit re-eval triggers. Queue should not grow beyond this between reviews. | `queue/pending-review.md` |
| **`@release-manager` cleanup hygiene gap** (11 stale merged remote branches) | Vertical C retro §4.6 flagged. Not a queue entry yet; awaiting one more cycle of evidence. **Action this review**: surface in retro; defer formal capture until next horizontal review. | This retro §7 |
| **L1 surface (`~/.codeium/windsurf/`, `~/.windsurf/`) unversioned** | ADR-008 step-8 gate emits manual warnings rather than enforcing for these paths. Trade-off accepted; entry #2 (M-2) tracks the eventual decision. | Queue entry #2 |
| **`global_rules.md` budget** | Currently 5822/6000 chars per `pre-sprint-2-review-4fa159.md` §B.1. Adding the `obsidian-context-priming` rule's concise version (Phase 2.5) must keep total ≤6000. Verify `wc -c` after edit. | Phase 2.5 acceptance check |
| **Milestone close PR for this retro** | This retro itself needs a close PR after Phase 2 + Phase 3. Pattern follows `sprint-1-5.md` and `sprint-2-vertical-c.md`. | Phase 3 closure commit |

## 8 — Definition of done for this retro

- [x] All `## Friction / surprises` items have a learning or a "won't change" rationale (folded into §2 + §7; Vertical C retro §4 covers milestone-friction)
- [x] All triage decisions documented per item (§3)
- [x] L1 proposals listed with target artifact path (§4)
- [x] ADRs written this milestone catalogued with reserved numbers (§5)
- [x] Retroactive-ADR check performed (§6)
- [x] Carry-forward populated (§7)
- [ ] All 6 Phase 2 promotions landed (PRs merged) — Phase 2 in progress
- [ ] Clean-tree gate (ADR-008) passed for `cascade-system/`; `~/.codeium/windsurf/` + `~/.windsurf/` warnings acknowledged — Phase 3
- [ ] `Status: closed` + `Date closed: <YYYY-MM-DD>` filled — Phase 3
- [ ] Final report block (`@sprint-review` step 11) emitted to user — Phase 3

## 9 — Sign-off (Phase 3, deferred)

Filled in at retro close after all Phase 2 promotions land + ADR-008 clean-tree gate passes.

---

*Template source: `~/.windsurf/templates/_shared/retro-template.md`. Drained by `@sprint-review`. L1 proposals handed off to `@update-horizontal` / `@write-skill`.*
