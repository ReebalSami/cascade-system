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

