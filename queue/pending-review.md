# Pending sprint-review queue

Drained by `@sprint-review`. Anything tagged `#capture` in chat (via `capture-signal` rule, once it lands) appends here.

Format:

```
## YYYY-MM-DD — <project> — <cascade>
- Insight: ...
- Source: <conversation-id or commit-sha>
- Proposed L1 change: ...
```

## Sprint 1 — cascade-system — Cascade A — M1.1

- **Insight**: 4 portfolio-website rules were considered for L1 promotion but deferred during M1.1 — each because its principle is captured elsewhere or its scope is L3-template-specific.
- **Source**: M1.1 issue #2 closure, this conversation
- **Proposed L1 changes** (re-evaluate at next @sprint-review):
  - `skill-by-skill` — currently deferred; re-evaluate if context-overload friction surfaces in any vertical Cascade. Trigger for promotion: a vertical reports "I started multiple things and lost focus."
  - `local-demo-before-push` — absorbed into `make-sure-it-works`; re-evaluate if make-sure-it-works proves too abstract and concrete UI-demo discipline needs its own rule.
  - `config-yaml-first` — defer to L3 nextjs-app template (future trigger sprint, when the first web project starts).
  - `github-project-management` — defer to `@sync-github` skill (M1.8). If `@sync-github` ends up duplicating logic across repos, extract a rule.

## Sprint 1 review — cascade-system — Cascade A — M-2 (L1 storage)

- **Insight**: All L1 components live in `~/.windsurf/` which is not a git repo. No diff history, no remote backup for skills / rules / workflows / contract / templates. Plan §3.10 left the L1 ↔ repo linkage as TBD; Sprint 1's heavy authoring (14 rules, 8 skills, 4 workflows, 1 contract, 2 templates, 12-file shared scaffold) made the gap acute.
- **Source**: `docs/reviews/sprint-1-review.md` §3.2 M-2 + `docs/decisions/ADR-008` (which notes the gate skips `~/.windsurf/` until this is resolved)
- **Proposed L1 change**: Decide a storage strategy via a focused `@grill-me` session. Candidate options:
  - (a) Init `~/.windsurf/` as its own git repo with GitHub remote (e.g., `ReebalSami/dotwindsurf`) — cleanest separation, two repos to keep in sync
  - (b) Move L1 into `cascade-system/l1/` with symlink back to `~/.windsurf/` — one repo, symlink fragility on some tools
  - (c) Sync script pattern — fragile, deferred
  - (d) Other
- **Blocker for**:
  - `@update-horizontal` — its "apply L1 changes" step is currently an unversioned file edit
  - `@sprint-review` step 8 (ADR-008 clean-tree gate) — currently skips `~/.windsurf/` with a warning; full enforcement needs the repo to exist
- **Scope note**: Sprint 2 Verticals B + C do **not** modify L1 directly, so they can proceed without this resolved. Prioritize ahead of Sprint 3 or when `@update-horizontal` gets its first real invocation.

## Sprint 1 review — cascade-system — Cascade A — queue archive policy

- **Insight**: `@sprint-review` step 10 (queue drain) says "Dropped → archive to `~/Projects/cascade-system/queue/archive/<sprint>.md` (create if missing)". The archive directory does not yet exist and no sprint has exercised the drop-to-archive path.
- **Source**: `~/.codeium/windsurf/skills/sprint-review/SKILL.md` step 10
- **Proposed L1 change**: Create `queue/archive/.gitkeep` when first archived entry lands, or during Sprint 2's first `@sprint-review`. No action needed until exercised.
- **Trigger for promotion**: First Sprint 2 `@sprint-review` that drops an item.

## Sprint 2 prep — cascade-system — Cascade A — brainstorm drift correction (ADR-018)

- **Insight**: The `parked-items-brainstorm.md` (commit `fa48ea2`, 2026-04-30) made two errors that ADR-018 corrected during dispatch: (1) §2.3 specified `know-your-hardware` would have a section in `global_rules.md`; (2) §3.3 specified `model_decision` activation in the global file. Both are wrong per Windsurf docs (`https://docs.windsurf.com/windsurf/cascade/memories`) — `global_rules.md` is always-on with no per-rule activation modes; the `(model_decision)` annotations there are decorative; only workspace rules support real `model_decision`. The architectural fix landed in ADR-018: `know-your-hardware` is workspace-deployed only.
- **Source**: ADR-018 §"Brainstorm drift correction"; `parked-items-brainstorm.md` §2.3 + §3.3
- **Empirical-test signal for ADR-017**: This is the brainstorm's first-dispatch artifact. The dispatch (Phase A of `~/.windsurf/plans/build-parked-items-6984b3.md`) caught the drift before it shipped — useful evidence that the brainstorm-then-dispatch pattern surfaces errors the brainstorm itself missed. Future Cascade authoring brainstorms should treat the dispatch step as a re-validation gate, not a rubber-stamp.
- **Proposed L1 change** (re-evaluate at Sprint 2 `@sprint-review`):
  - Should `@propose-extension` step 8 (route + ADR-draft presentation) include a "platform constraint check" sub-step that verifies any global_rules.md / activation-mode claims against current Windsurf docs? Possibly via a `context7` MCP query as a routine pre-flight.
  - Should the brainstorm template add a "platform-constraints checklist" section so future brainstorms catch these without dispatch-time correction?
- **Trigger for promotion**: Either at next Sprint 2 `@sprint-review`, or earlier if a similar dispatch-time correction is caught in another build.

## Sprint 2 — cascade-system — Cascade A — release-discipline cluster gap (`/start-project` step 9 dangling reference) — **RESOLVED by PR #14 / issue #18**

> **Sprint 1.5 retro decision (2026-05-02)**: Resolved during PR #14 (G6) which inserted branch-protection as step 11 of `/start-project.md`, renumbered subsequent steps, and aligned the 4 cross-references. Retro-filed as issue #18 (Sprint 1.5.9). Entry retained for archaeological value; will move to `queue/archive/sprint-1-5.md` when archive policy activates on first true drop.

- **Insight**: ADR-018 §"Branch-protection on `main`" (line 84) claims `/start-project` step 9 was "a new step added by this ADR" that invokes `gh api ... /branches/main/protection`. Two downstream cross-references propagate the claim: `~/.codeium/windsurf/skills/release-manager/SKILL.md:116` (*"called from `/start-project` step 9"*) and `~/Projects/cascade-system/docs/rules/branch-and-pr-required.md:68` (*"`/start-project` step 9 sets `main` branch protection"*). But `~/.codeium/windsurf/global_workflows/start-project.md:120-136` step 9 is currently "Initial commit" (the `/commit` invocation) — the branch-protection step was **never actually added** to the workflow. The cluster shipped with rule + skill + 4 workflows but the cross-cutting `/start-project` edit was missed during Phase A dispatch.
- **Source**: Three-question audit on 2026-04-30 (`~/.windsurf/plans/three-question-audit-475e93.md`); affected files cited above.
- **Proposed fix** (Sprint 2 punch-list, before any retro can claim the cluster is "complete"): Add the branch-protection step to `/start-project.md` *after* the cold-start push (current step 10) and *before* Project v2 creation (current step 11) — branch-protection requires `main` to exist on the remote first. Insert as new step 11 with the single-line `gh api -X PUT repos/<owner>/<repo>/branches/main/protection ...` call; on HTTP 403 (free-tier private repo), skip-with-warning per ADR-018 §"Branch-protection on `main`". Renumber existing steps 11→12, 12→13, 13→14, 14→15. Update the three cross-references to point to "step 11" (was "step 9").
- **Acceptable alternative**: keep ADR-018's "step 9" language and reorder the workflow body so the new step lands at position 9. Worse — larger churn, breaks the natural "create → commit → push → protect → project" reading order.
- **Why-deferred**: Phase A landed the rule + skill + workflows; the cluster functions correctly without branch-protection (the agent-side rule is the enforcement; server-side protection is a nice-to-have that's also impossible on free-tier private repos). The fix is a follow-up edit, not a Phase A blocker. Per ADR-018 §"Self-test plan" intent (*"If `@release-manager` fails to orchestrate Phase B, that's a Phase A bug; capture inline"*).
- **Trigger for promotion**: Sprint 2 punch-list cleanup before any sprint retro that would otherwise mark the release-discipline cluster as proven.
- **Caveat on auto-fire**: `/start-project.md:113` uses `gh repo create ... --push`, where the cold-start push happens as a `gh` side-effect — Cascade itself doesn't invoke `git push`, so the `branch-and-pr-required` self-check never gets a runtime trigger to verify. The cold-start exception covers this semantically (all 3 conditions hold), but the rule's pre-flight checklist is bypassed. Worth retro discussion.

## Sprint 1.5 — cascade-system — Cascade A — ADR-006 path drift discovered during 1.5.6

- **Insight**: While verifying the planned 3-file scope of issue 1.5.6, grep across `~/.windsurf/contracts/`, `~/.codeium/windsurf/global_workflows/`, and `cascade-system/docs/` surfaced two additional live-drift references in `docs/decisions/ADR-006-skill-frontmatter-schema.md`: line 45 (the `name` field constraint) pointed to dead `~/.windsurf/skills/<name>/SKILL.md`, and line 77 (workflow-schema consequence) pointed to dead `~/.windsurf/workflows/<name>.md`. Because ADR-006 is the schema contract that `@write-skill` consumes when authoring new skills, the drift would have misdirected the soon-to-be-authored `@begin` (1.5.1) and `@kickoff` (1.5.2) skill placements if left unfixed.
- **Source**: 1.5.6 verification grep, this conversation (`f8add2`), 2026-05-01.
- **Action taken in 1.5.6**: Both ADR-006 lines were fixed in the same PR as the planned 3-file scope, with a citation to ADR-014 (line 45) and ADR-014/016 (line 77). Per `bidirectional-learning-pipe`, the in-flight scope expansion is captured here rather than left silent.
- **Proposed L1 change** (re-evaluate at Sprint 1.5 `@sprint-review`):
  - Add a one-time path-drift sweep to `@verify-l1` that greps for `~/.windsurf/skills/`, `~/.windsurf/workflows/`, and `~/.windsurf/rules/` literally across cascade-system docs + L1 contracts/templates and reports any hit not already inside a historical-marker note. The grep itself is mechanical; the false-positive filter (allow ADRs / manuals / rule archives that intentionally cite the dead paths as anti-patterns) is the harder part.
  - Decide whether `@docs-refresh` should also include this lint, or whether `@verify-l1` is the better home (current lean: `@verify-l1` since it already audits L1 path correctness).
- **Trigger for promotion**: Sprint 1.5 `@sprint-review` (1.5.11) — surface as L1-promotion candidate after authoring `@begin`/`@kickoff` proves the pattern of consulting ADR-006 from within `@write-skill` flows.

## Sprint 2 prep — cascade-system — Cascade A — vertical-spawn bootstrap gap — **RESOLVED by Sprint 1.5.1 (`@begin`, ADR-019) + 1.5.2 (`@kickoff`, ADR-020)**

> **Sprint 1.5 retro decision (2026-05-02)**: This entry was the foundational driver for Sprint 1.5 itself. Resolved by the paired ADRs ADR-019 (`@begin` for new-idea path) + ADR-020 (`@kickoff` for vertical-pickup path). Legacy `~/.windsurf/plans/<vertical>-kickoff-*.md` files retained per ADR-011. Live-fire test queued for first Sprint 2 vertical session — empirical-test footnotes in both ADRs specify what regression looks like (any reach for legacy kickoff plan instead of `@kickoff`). Entry retained for archaeological value; will move to `queue/archive/sprint-1-5.md` when archive policy activates on first true drop.

- **Insight**: `/start-project` is project-bootstrap-only; runs once per project (cascade-system already consumed its run at Sprint 0). `/run-phase` reads `phases.yaml` and is L3-project-scoped per the phase-taxonomy contract — the cascade-system meta-repo has no `phases.yaml`. There is **no L1 workflow for spawning a vertical Cascade mid-sprint**. Verticals are spawned by hand-authored kickoff plans at `~/.windsurf/plans/<vertical>-kickoff-<suffix>.md` that the user pastes into a fresh Cascade window. The motions `/start-project` step 14 bakes in for new projects (research → brainstorm via `@grill-me` → handoff) do not transfer to mid-project sprint work. `@grill-me` exists and *should* auto-activate by description-match on "starting a new feature/phase", but the kickoff prompts have not been naming it explicitly, so the path stays implicit. Discovered during cascade-c-obsidian kickoff prep when the user asked whether the motions should be in `/start-project` (they aren't, and shouldn't be — different layer of concern).
- **Source**: This conversation (cascade-c-obsidian kickoff prep, 2026-05-01); `~/.codeium/windsurf/global_workflows/start-project.md:1-263`; `~/.windsurf/contracts/phase-taxonomy.md:23` (phase sets are L3-project-scoped); existing kickoff plans `~/.windsurf/plans/cascade-{b,c}-*-kickoff-*.md`.
- **Proposed L1 change** (re-evaluate at next `@sprint-review`):
  - Author `/start-sprint <vertical>` global workflow that: (1) reads the meta-repo's active plan; (2) identifies the active sprint + vertical from §4 plan structure; (3) generates a kickoff prompt naming the read-list, mandatory channels (`@propose-extension`, `@release-manager`), `@grill-me` as the plan-stress-test gate, and the hard-gate before execution; (4) writes it to `~/.windsurf/plans/<vertical>-kickoff-<suffix>.md` for the user to paste. Treats vertical-spawning as first-class L1 infrastructure rather than ad-hoc artifact authoring.
  - Alternative: extend `/start-project` to take a `--mode=vertical-pickup` flag that skips bootstrap steps (1–13) and runs only kickoff-prompt generation. Worse — overloads a workflow that has a clear single purpose.
- **Tactical fix landed pre-promotion**: 2026-05-01 edit to `~/.windsurf/plans/cascade-c-obsidian-kickoff-4fa159.md` adds a research-broadened step 2 + `@grill-me` stress-test step 4 to the cascade-c kickoff prompt. Confirms the shape of motions a `/start-sprint` workflow would generate. If/when promoted, the workflow's prompt template should mirror this structure.
- **Blocker for**: Nothing — current hand-authored flow works. This is process-quality / discoverability improvement, not a bug.
- **Trigger for promotion**: After Sprint 2 closes with three vertical spawns (B, C, D) all hand-authored. The friction of writing each one + the recurring shape (always read-list, always mandatory channels, always hard-gate) will be the empirical case for promotion.

