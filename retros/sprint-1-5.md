# Sprint 1.5 — Onboard Ramp — Retro

**Status**: closed
**Sprint**: 1.5 (interstitial hardening between Sprint 1 close and Sprint 2 open)
**Cascade**: A (meta-system author)
**Plan**: `~/.windsurf/plans/sprint-1-5-onboard-ramp-f8add2.md`
**Milestone**: GitHub `Sprint 1.5 — Onboard Ramp` (#1)
**Issues**: 15 (4 retroactive #17–#20 + 11 forward #21–#31, all closed by sprint close)
**PRs**: 8 merged into `main` via `@release-manager` discipline (#32–#39)
**Date**: 2026-05-02

## 1 — Summary

Sprint 1.5 closed the four problem clusters identified in the audit conversation that produced the plan file:

1. **Front-door gap** → built `@begin` (1.5.1, ADR-019) for new-idea project starts and `@kickoff` (1.5.2, ADR-020) for vertical Cascade pickups. Together they obsolete hand-authored kickoff plans at `~/.windsurf/plans/<vertical>-kickoff-*.md` (legacy retained per ADR-011 supersession-over-deletion).
2. **Discoverability gap** → authored `docs/cheat-sheet.md` (1.5.3), `docs/skills/INDEX.md` + `docs/workflows/INDEX.md` (1.5.4), and `docs/architecture/system-overview.mmd` (1.5.5). Updated `@docs-refresh` to maintain the two new pointer indexes. Updated `cascade-system/AGENTS.md` (1.5.8) as the always-on first-touch pointer that surfaces all Sprint 1.5 artifacts.
3. **Project bookkeeping drift** → filed Sprint 1.5 milestone, created `chore` + `retroactive` labels, retro-filed 4 issues for already-merged PRs #13–#16 (1.5.9), added all 15 issues to GitHub Project #5. The four naked PRs are no longer naked.
4. **Workspace-root drift** → rewrote `SETUP_ROADMAP.md` (1.5.7) as a 31-line workspace pointer, replacing a 213-line file that predated cascade-system by one day. Added drift fixes (1.5.6) for `phase-taxonomy.md`, `start-project.md`, `sprint-1-review.md`, plus an in-flight ADR-006 path-drift discovery.

A fifth concern surfaced inside the sprint: the deferred manual.md split decision (1.5.10). Resolved as **no split** with ADR-021; manual stays single-file narrative complementing the cheat-sheet's scannable inventory.

**Verdict**: All 11 plan items shipped. Sprint 2 is unblocked. The next live-fire test is the first post-sprint vertical pickup or new-project start.

## 2 — What worked

**`@release-manager` discipline held across 8 PRs.** Every PR routed through `/branch-start` → `/commit` → `/branch-push-and-pr` → squash-merge, no direct pushes to `main`. The pattern that broke during Sprint 1 hardening (PR #13–#16 merged without milestones or covering issues) did not re-occur. Issue cross-linking via `closes #N` keywords auto-closed issues on merge.

**Plan-file-as-spec was sufficient for execution.** The 595-line plan at `~/.windsurf/plans/sprint-1-5-onboard-ramp-f8add2.md` carried enough detail (acceptance criteria, sources to consult, dependencies, sequencing) that execution proceeded without re-interviewing the user. Forward references to artifacts not yet authored (e.g., the cheat-sheet referencing `@begin`/`@kickoff` before 1.5.1/1.5.2 landed) resolved cleanly as later issues filled in.

**ADR-009 numbering protocol held.** Reserved ADR-019 in INDEX before authoring ADR-019 (1.5.1); same for ADR-020 (1.5.2) and ADR-021 (1.5.10). No ADR-numbering conflicts despite all three being authored in sequence within the same sprint.

**Bidirectional learning pipe surfaced one in-flight finding.** During 1.5.6's drift-fix verification grep, two additional dead-path references in `ADR-006` (line 45 + line 77) were discovered that weren't in the original plan §3 1.5.6 scope. Per `bidirectional-learning-pipe`, the in-flight scope expansion was captured to `queue/pending-review.md` rather than rolled in silently. The expansion was applied in the same PR (since `@write-skill` consumes ADR-006 and would have misdirected `@begin`/`@kickoff` if left); rationale documented in commit body. Queue entry survives for next review.

**Cheat-sheet → INDEXes → diagram → AGENTS.md ordering paid off.** Building scannable inventory first (1.5.3) clarified what the indexes (1.5.4) and diagram (1.5.5) needed to cover. AGENTS.md (1.5.8) then absorbed the pointer-listing duty cleanly, freeing manual.md (1.5.10) to stay narrative-only. Sequenced refactor without circular rewrites.

**The 4 helper workflows behaved.** `/branch-start`, `/branch-push-and-pr`, `/ci-watch`, `/branch-merge-and-cleanup` ran cleanly on every PR. `/ci-watch` correctly exited "no CI configured" on every PR (cascade-system has no CI by design). `/commit` tempfile pattern bypassed the macOS-Windsurf newline crash on every commit-with-body.

## 3 — What surfaced (unplanned discoveries)

**ADR-006 path drift inside an ADR.** Discovered during 1.5.6 grep verification. `ADR-006-skill-frontmatter-schema.md` (the schema contract `@write-skill` consumes) had two lines referencing dead `~/.windsurf/skills/` and `~/.windsurf/workflows/` paths from before ADR-014 + ADR-016 landed. Fixed in the same PR; captured to queue. **Insight**: `@verify-l1`'s scope should arguably extend beyond runtime-active paths to include canonical-path drift in cascade-system docs themselves — a `@verify-l1`-runs-grep pass would have caught this earlier. Promotion candidate; see §4.

**SETUP_ROADMAP.md was older than cascade-system.** The 213-line workspace-root file dated 2026-04-28; cascade-system Sprint 0 was 2026-04-29. The file actively misled by referencing dead `~/.windsurf/rules.md` and "L3 templates not built yet" claims that no longer held. **Insight**: workspace-root files outside any git repo are blind spots — they don't show up in `@docs-refresh` placement audits because they aren't inside `docs/`. Worth a queue entry for a future "workspace-root sweep" on `~/Projects/*.md`.

**4 PRs had landed unmilestoned.** The audit surfaced PRs #13–#16 (release-discipline cluster + post-Sprint-1 punch-list + cascade-c handoff refresh + queue capture) had been merged between 2026-04-30 and 2026-05-01 with no GitHub milestone, no covering issues, no Project board representation. **Insight**: between-sprint windows are when bookkeeping discipline lapses; the pre-Sprint-2 hardening period should have either (a) opened Sprint 2 prep milestone immediately, or (b) opened "Sprint 1.5" at the moment of intent. The retroactive filing in 1.5.9 corrected this.

**Manual.md split needed only minor trim.** Pre-decision lean was "remove duplicates; keep single file"; post-cheat-sheet inspection confirmed the split would have produced 4-5 files of 30-50 lines each — worse navigation, broken cross-section narrative bridges. Trim was 10 lines (`§"Pointers"` collapsed to 2 cross-reference paragraphs). The decision-with-low-trim outcome strengthens the case that keeping options open until the dependent artifact lands (1.5.3 cheat-sheet) prevents premature splits.

## 4 — Queue drain decisions

`cascade-system/queue/pending-review.md` had 6 entries at sprint start; 1 added in-flight (Sprint 1.5.6 ADR-006 finding). Decisions:

| Queue entry | Decision | Rationale |
|---|---|---|
| Sprint 1 — M1.1 deferrals (4 candidate rules) | **Defer to Sprint 2 retro** | Sprint 1.5 produced no new evidence on `skill-by-skill`, `local-demo-before-push`, `config-yaml-first`, `github-project-management`. Sprint 2 verticals will. |
| Sprint 1 review — M-2 (L1 storage) | **Defer to next review** | Out of Sprint 1.5 scope. M-2 is its own dedicated micro-sprint when activated. The 8 PRs in Sprint 1.5 confirmed the gap is real (every L1 update — `@docs-refresh` SKILL.md edit in 1.5.4, `@begin` SKILL.md in 1.5.1, etc. — went outside the diff) but did not change the trade-off space. |
| Sprint 1 review — queue archive policy | **Defer** | No archive run yet; trigger is "first dropped item". This sprint had no drops. |
| Sprint 2 prep — brainstorm drift correction (ADR-018) | **Defer to Sprint 2 retro** | Awaits Sprint 2 dispatch evidence. Sprint 1.5 didn't author new brainstorms. |
| Sprint 2 — release-discipline cluster gap (`/start-project` step 9) | **Mark as resolved by PR #14 / issue #18** | Already fixed during PR #14 (G6). Move to archive when archive policy activates. |
| Sprint 1.5 — ADR-006 path drift (in-flight 1.5.6 finding) | **Promote to L1 candidate; ACTION at next milestone** | Proposed `@verify-l1` enhancement: add a path-drift sweep step that greps for dead `~/.windsurf/skills/`, `~/.windsurf/workflows/`, `~/.windsurf/rules/` literals across cascade-system docs and L1 contracts/templates, with a false-positive filter for legitimate ADR/manual citations. Not urgent (Sprint 1.5.6 fixed the present drift); but a recurring-mode prevention worth `@update-horizontal`-ing in Sprint 2 prep. |
| Sprint 2 prep — vertical-spawn bootstrap gap | **Mark as resolved by Sprint 1.5.1 (`@begin`) + 1.5.2 (`@kickoff`)** | This entry was the foundational driver for Sprint 1.5 itself. Closed by the two skill ADRs (019 + 020). Move to archive when archive policy activates. |

**Net L1 promotions ready to hand off to `@update-horizontal`**: zero urgent, one queued (`@verify-l1` path-drift sweep). The queued item stays in `pending-review.md` for action in Sprint 2 prep or as a free-time chore — not an exit blocker for Sprint 1.5.

## 5 — Sprint 2 unblock check

The audit's stated exit criterion was *"`@sprint-review` runs against Sprint 1.5 milestone and writes `cascade-system/retros/sprint-1-5.md`. The retro lists L1 promotions (if any) and confirms Sprint 2 is unblocked."*

Sprint 2 unblock status:

- ✅ Front-door entry skills exist (`@begin`, `@kickoff`) for the next vertical pickups
- ✅ Cheat-sheet + INDEXes + diagram + AGENTS.md surface ready for fresh Cascades
- ✅ Drift fixes applied across 5 files (3 listed in plan + 2 ADR-006 surfaces in-flight)
- ✅ Workspace pointer (`SETUP_ROADMAP.md`) honest
- ✅ Project bookkeeping repaired (4 retroactive issues + Sprint 1.5 milestone) — `@sync-github` confirms all 15 in board state Done by sprint close
- ✅ Cleanup ADRs landed (019 + 020 + 021)
- ✅ Queue drained with explicit decisions per entry

**Sprint 2 is unblocked.** The next vertical pickup (likely cascade-c-obsidian or a Sprint 2A.x release-discipline-cluster Phase B follow-up) is the live-fire test for `@kickoff`. The next new-project start is the live-fire test for `@begin`. Empirical-test footnotes in ADR-019 and ADR-020 specify what regression looks like.

## 6 — Action items for next review

| Action | Owner | When |
|---|---|---|
| Live-fire `@begin` on the next new-project start; observe whether it eliminates kickoff-plan authoring | next horizontal Cascade | First Sprint 2+ project start |
| Live-fire `@kickoff` on the first Sprint 2 vertical pickup; observe whether the user invokes it or reaches for the legacy kickoff plan | next horizontal Cascade | First Sprint 2 vertical session |
| Action `@verify-l1` path-drift sweep promotion (queue entry from 1.5.6) | `@update-horizontal` via `@sprint-review` | Sprint 2 prep or earlier free-time |
| Decide L1 storage strategy (M-2) | dedicated micro-sprint or Sprint 3 | When M-2 friction recurs |
| Run `@sync-github` periodically during Sprint 2 to prevent recurrence of the Sprint-1-to-Sprint-1.5 naked-PR window | horizontal Cascade | Periodic during active work |

## 7 — Sign-off

Sprint 1.5 closed cleanly. Working tree is clean (this retro is the final commit). All 15 milestone issues are in `state: closed`. The 8 PRs that landed Sprint 1.5 work are squash-merged with covering issues + milestone tags. Queue drained.

The next sprint opens fresh. `@kickoff` will read this retro, the plan file, and the cheat-sheet when its first invocation lands.
