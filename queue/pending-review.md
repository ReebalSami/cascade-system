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

## Sprint 2 — cascade-system — Cascade A — release-discipline cluster gap (`/start-project` step 9 dangling reference)

- **Insight**: ADR-018 §"Branch-protection on `main`" (line 84) claims `/start-project` step 9 was "a new step added by this ADR" that invokes `gh api ... /branches/main/protection`. Two downstream cross-references propagate the claim: `~/.codeium/windsurf/skills/release-manager/SKILL.md:116` (*"called from `/start-project` step 9"*) and `~/Projects/cascade-system/docs/rules/branch-and-pr-required.md:68` (*"`/start-project` step 9 sets `main` branch protection"*). But `~/.codeium/windsurf/global_workflows/start-project.md:120-136` step 9 is currently "Initial commit" (the `/commit` invocation) — the branch-protection step was **never actually added** to the workflow. The cluster shipped with rule + skill + 4 workflows but the cross-cutting `/start-project` edit was missed during Phase A dispatch.
- **Source**: Three-question audit on 2026-04-30 (`~/.windsurf/plans/three-question-audit-475e93.md`); affected files cited above.
- **Proposed fix** (Sprint 2 punch-list, before any retro can claim the cluster is "complete"): Add the branch-protection step to `/start-project.md` *after* the cold-start push (current step 10) and *before* Project v2 creation (current step 11) — branch-protection requires `main` to exist on the remote first. Insert as new step 11 with the single-line `gh api -X PUT repos/<owner>/<repo>/branches/main/protection ...` call; on HTTP 403 (free-tier private repo), skip-with-warning per ADR-018 §"Branch-protection on `main`". Renumber existing steps 11→12, 12→13, 13→14, 14→15. Update the three cross-references to point to "step 11" (was "step 9").
- **Acceptable alternative**: keep ADR-018's "step 9" language and reorder the workflow body so the new step lands at position 9. Worse — larger churn, breaks the natural "create → commit → push → protect → project" reading order.
- **Why-deferred**: Phase A landed the rule + skill + workflows; the cluster functions correctly without branch-protection (the agent-side rule is the enforcement; server-side protection is a nice-to-have that's also impossible on free-tier private repos). The fix is a follow-up edit, not a Phase A blocker. Per ADR-018 §"Self-test plan" intent (*"If `@release-manager` fails to orchestrate Phase B, that's a Phase A bug; capture inline"*).
- **Trigger for promotion**: Sprint 2 punch-list cleanup before any sprint retro that would otherwise mark the release-discipline cluster as proven.
- **Caveat on auto-fire**: `/start-project.md:113` uses `gh repo create ... --push`, where the cold-start push happens as a `gh` side-effect — Cascade itself doesn't invoke `git push`, so the `branch-and-pr-required` self-check never gets a runtime trigger to verify. The cold-start exception covers this semantically (all 3 conditions hold), but the rule's pre-flight checklist is bypassed. Worth retro discussion.

