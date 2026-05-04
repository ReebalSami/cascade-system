# Archive — Sprint 1.5 + earlier entries archived during Sprint 2 Vertical C drain

> Archived 2026-05-04 by `@sprint-review` against the Sprint 2 Vertical C closed milestone. Drain decisions documented in `~/Projects/cascade-system/retros/sprint-2-vertical-c-drain.md` §3. This is the first archive file produced by the queue-archive policy (queue entry #3, activated on first drop per its own trigger condition).
>
> Each entry below was either **DROPPED** (no longer worth tracking; principle absorbed elsewhere or evidence proved the concern speculative) or **RESOLVED** (action landed in a PR; entry retained for archaeological value at the time of resolution and now moved out of the live queue).

---

## DROPPED — M1.1 / `skill-by-skill` (Sprint 1 deferred portfolio-website rule)

**Drop date**: 2026-05-04 (Sprint 2 Vertical C drain)
**Decision**: DROP — no focus-loss friction surfaced across Sprint 1, Sprint 1.5, or Sprint 2 Vertical C. Original concern was speculative based on portfolio-website's pre-cascade-system rule set. Coverage exists via `no-half-knowledge` (read files fully, map before editing) and `make-sure-it-works` (one acceptance criterion at a time).
**Original entry text** (from `pending-review.md` parent §"Sprint 1 — cascade-system — Cascade A — M1.1" sub-item 1):

- `skill-by-skill` — currently deferred; re-evaluate if context-overload friction surfaces in any vertical Cascade. Trigger for promotion: a vertical reports "I started multiple things and lost focus."

**Rationale**: Three sprints worth of vertical/horizontal Cascade work (Sprint 1 = 12 milestones; Sprint 1.5 = 11 milestones; Sprint 2 Vertical C = 6 milestones) ran without producing the trigger condition. The `@kickoff` ADR-020 + `@begin` ADR-019 front-door skills additionally enforce single-scope discipline at session start. The risk this rule was hedging against is now structurally mitigated.

---

## DROPPED — M1.1 / `local-demo-before-push` (Sprint 1 deferred portfolio-website rule)

**Drop date**: 2026-05-04 (Sprint 2 Vertical C drain)
**Decision**: DROP — principle absorbed into `make-sure-it-works` ("evidence over claims; run lint/build/test/demo before declaring done"). Concrete UI-demo discipline (the original portfolio-website-flavored variant) is L3-template territory; portfolio-website's own `.windsurf/rules/` carries it. Promoting it to L1 would override what L3 templates already do correctly.
**Original entry text** (from `pending-review.md` parent §"Sprint 1 — cascade-system — Cascade A — M1.1" sub-item 2):

- `local-demo-before-push` — absorbed into `make-sure-it-works`; re-evaluate if make-sure-it-works proves too abstract and concrete UI-demo discipline needs its own rule.

**Rationale**: `make-sure-it-works` has been exercised across all three sprints (1, 1.5, 2C) without surfacing the "too abstract" failure mode. L3 templates with UI concerns (portfolio-website, future nextjs-app) keep their concrete demo-discipline rules at the L2/L3 layer where they belong.

---

## RESOLVED — Release-discipline cluster gap (`/start-project` step 9 dangling reference)

**Resolution date**: 2026-05-02 (Sprint 1.5 PR #14 + retro-filed issue #18)
**Original status**: RESOLVED by PR #14 (G6); entry retained for archaeological value pending archive-policy activation.
**Original entry text** (from `pending-review.md` §"Sprint 2 — cascade-system — Cascade A — release-discipline cluster gap"):

> **Sprint 1.5 retro decision (2026-05-02)**: Resolved during PR #14 (G6) which inserted branch-protection as step 11 of `/start-project.md`, renumbered subsequent steps, and aligned the 4 cross-references. Retro-filed as issue #18 (Sprint 1.5.9). Entry retained for archaeological value; will move to `queue/archive/sprint-1-5.md` when archive policy activates on first true drop.

- **Insight**: ADR-018 §"Branch-protection on `main`" (line 84) claims `/start-project` step 9 was "a new step added by this ADR" that invokes `gh api ... /branches/main/protection`. Two downstream cross-references propagate the claim: `~/.codeium/windsurf/skills/release-manager/SKILL.md:116` (*"called from `/start-project` step 9"*) and `~/Projects/cascade-system/docs/rules/branch-and-pr-required.md:68` (*"`/start-project` step 9 sets `main` branch protection"*). But `~/.codeium/windsurf/global_workflows/start-project.md:120-136` step 9 is currently "Initial commit" (the `/commit` invocation) — the branch-protection step was **never actually added** to the workflow. The cluster shipped with rule + skill + 4 workflows but the cross-cutting `/start-project` edit was missed during Phase A dispatch.
- **Source**: Three-question audit on 2026-04-30 (`~/.windsurf/plans/three-question-audit-475e93.md`); affected files cited above.
- **Proposed fix** (Sprint 2 punch-list, before any retro can claim the cluster is "complete"): Add the branch-protection step to `/start-project.md` *after* the cold-start push (current step 10) and *before* Project v2 creation (current step 11) — branch-protection requires `main` to exist on the remote first. Insert as new step 11 with the single-line `gh api -X PUT repos/<owner>/<repo>/branches/main/protection ...` call; on HTTP 403 (free-tier private repo), skip-with-warning per ADR-018 §"Branch-protection on `main`". Renumber existing steps 11→12, 12→13, 13→14, 14→15. Update the three cross-references to point to "step 11" (was "step 9").
- **Acceptable alternative**: keep ADR-018's "step 9" language and reorder the workflow body so the new step lands at position 9. Worse — larger churn, breaks the natural "create → commit → push → protect → project" reading order.
- **Why-deferred**: Phase A landed the rule + skill + workflows; the cluster functions correctly without branch-protection (the agent-side rule is the enforcement; server-side protection is a nice-to-have that's also impossible on free-tier private repos). The fix is a follow-up edit, not a Phase A blocker. Per ADR-018 §"Self-test plan" intent (*"If `@release-manager` fails to orchestrate Phase B, that's a Phase A bug; capture inline"*).
- **Trigger for promotion**: Sprint 2 punch-list cleanup before any sprint retro that would otherwise mark the release-discipline cluster as proven.
- **Caveat on auto-fire**: `/start-project.md:113` uses `gh repo create ... --push`, where the cold-start push happens as a `gh` side-effect — Cascade itself doesn't invoke `git push`, so the `branch-and-pr-required` self-check never gets a runtime trigger to verify. The cold-start exception covers this semantically (all 3 conditions hold), but the rule's pre-flight checklist is bypassed. Worth retro discussion.

---

## RESOLVED — Vertical-spawn bootstrap gap

**Resolution date**: 2026-05-02 (Sprint 1.5.1 `@begin` + Sprint 1.5.2 `@kickoff`)
**Original status**: RESOLVED by ADR-019 + ADR-020; entry retained for archaeological value pending archive-policy activation.
**Original entry text** (from `pending-review.md` §"Sprint 2 prep — cascade-system — Cascade A — vertical-spawn bootstrap gap"):

> **Sprint 1.5 retro decision (2026-05-02)**: This entry was the foundational driver for Sprint 1.5 itself. Resolved by the paired ADRs ADR-019 (`@begin` for new-idea path) + ADR-020 (`@kickoff` for vertical-pickup path). Legacy `~/.windsurf/plans/<vertical>-kickoff-*.md` files retained per ADR-011. Live-fire test queued for first Sprint 2 vertical session — empirical-test footnotes in both ADRs specify what regression looks like (any reach for legacy kickoff plan instead of `@kickoff`). Entry retained for archaeological value; will move to `queue/archive/sprint-1-5.md` when archive policy activates on first true drop.

- **Insight**: `/start-project` is project-bootstrap-only; runs once per project (cascade-system already consumed its run at Sprint 0). `/run-phase` reads `phases.yaml` and is L3-project-scoped per the phase-taxonomy contract — the cascade-system meta-repo has no `phases.yaml`. There is **no L1 workflow for spawning a vertical Cascade mid-sprint**. Verticals are spawned by hand-authored kickoff plans at `~/.windsurf/plans/<vertical>-kickoff-<suffix>.md` that the user pastes into a fresh Cascade window. The motions `/start-project` step 14 bakes in for new projects (research → brainstorm via `@grill-me` → handoff) do not transfer to mid-project sprint work. `@grill-me` exists and *should* auto-activate by description-match on "starting a new feature/phase", but the kickoff prompts have not been naming it explicitly, so the path stays implicit. Discovered during cascade-c-obsidian kickoff prep when the user asked whether the motions should be in `/start-project` (they aren't, and shouldn't be — different layer of concern).
- **Source**: This conversation (cascade-c-obsidian kickoff prep, 2026-05-01); `~/.codeium/windsurf/global_workflows/start-project.md:1-263`; `~/.windsurf/contracts/phase-taxonomy.md:23` (phase sets are L3-project-scoped); existing kickoff plans `~/.windsurf/plans/cascade-{b,c}-*-kickoff-*.md`.
- **Proposed L1 change** (re-evaluate at next `@sprint-review`):
  - Author `/start-sprint <vertical>` global workflow that: (1) reads the meta-repo's active plan; (2) identifies the active sprint + vertical from §4 plan structure; (3) generates a kickoff prompt naming the read-list, mandatory channels (`@propose-extension`, `@release-manager`), `@grill-me` as the plan-stress-test gate, and the hard-gate before execution; (4) writes it to `~/.windsurf/plans/<vertical>-kickoff-<suffix>.md` for the user to paste. Treats vertical-spawning as first-class L1 infrastructure rather than ad-hoc artifact authoring.
  - Alternative: extend `/start-project` to take a `--mode=vertical-pickup` flag that skips bootstrap steps (1–13) and runs only kickoff-prompt generation. Worse — overloads a workflow that has a clear single purpose.
- **Tactical fix landed pre-promotion**: 2026-05-01 edit to `~/.windsurf/plans/cascade-c-obsidian-kickoff-4fa159.md` adds a research-broadened step 2 + `@grill-me` stress-test step 4 to the cascade-c kickoff prompt. Confirms the shape of motions a `/start-sprint` workflow would generate. If/when promoted, the workflow's prompt template should mirror this structure.
- **Blocker for**: Nothing — current hand-authored flow works. This is process-quality / discoverability improvement, not a bug.
- **Trigger for promotion**: After Sprint 2 closes with three vertical spawns (B, C, D) all hand-authored. The friction of writing each one + the recurring shape (always read-list, always mandatory channels, always hard-gate) will be the empirical case for promotion.

**Resolution shape**: ADR-019 authored `@begin` (new-idea entry path; orchestrates `@grill-me` → stack-decision → `/add-project-type` (if needed) → `/start-project` → `/run-phase brainstorm`). ADR-020 authored `@kickoff` (vertical-pickup path; reads handoff + parent plan + cited ADRs + cheat-sheet, detects lifecycle position, files missing milestones, asks ONE focused starting question). Together they obsoleted the hand-authored kickoff plan pattern. First live-fire test was Cascade C's M2C.6 invocation (`@kickoff cascade-system/docs/handoffs/cascade-c-obsidian.md`) per `retros/sprint-2-vertical-c.md` §5 — passed.

---
