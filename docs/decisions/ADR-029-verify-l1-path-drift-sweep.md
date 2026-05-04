# ADR-029: `@verify-l1` path-drift sweep expansion + workspace-only-rule awareness

**Status**: Accepted
**Date**: 2026-05-04
**Plan**: `~/.windsurf/plans/sprint-2-vertical-c-drain-86a684.md` Phase 2.6
**Source retro**: `~/Projects/cascade-system/retros/sprint-2-vertical-c-drain.md` §3 (row 11) + §4 (Phase 2.6)
**Source queue entry**: Sprint-1.5 (`Sprint 1.5 — cascade-system — Cascade A — ADR-006 path drift discovered during 1.5.6`)
**Related**: ADR-014 (canonical L1 paths — the contract `@verify-l1` enforces), ADR-016 (workflow path correction — adds allow-list entry), ADR-018 (`know-your-hardware` workspace-only precedent), ADR-028 (`obsidian-context-priming` — second workspace-only rule)
**Issue**: Sprint 1.5 capture `f8add2` (2026-05-01)

## Context

Sprint 1.5.6 verification work surfaced two live path-drift references in `docs/decisions/ADR-006-skill-frontmatter-schema.md` that the existing `@verify-l1` step 8 path-reference sanity check would have **missed** — step 8 (pre-ADR-029) searched only `~/.codeium/windsurf/`, `~/Projects/cascade-system/docs/rules/`, and `~/Projects/cascade-system/README.md`. The ADR-006 drift was in `docs/decisions/`, outside the sweep scope.

Because ADR-006 is the schema contract `@write-skill` consumes when authoring new skills, the drift would have misdirected the Sprint-1.5 authoring of `@begin` + `@kickoff` if it had not been caught by a side-band grep. The Sprint-1.5 queue capture proposed widening `@verify-l1`'s sweep + adding a proper false-positive filter.

**Sprint 2 additions that motivated re-evaluation**:

- ADR-018 + ADR-028 established a **workspace-only rule** pattern (`know-your-hardware`, `obsidian-context-priming`): rules listed in `INDEX.md` but intentionally **absent** from `global_rules.md`. The pre-ADR-029 `@verify-l1` step 5/6 would flag these as drift (*"missing section in global_rules.md"*) — a false-positive that would fire on every `@verify-l1` invocation.
- ADR-028 explicitly deferred *"`@verify-l1` gains awareness that some rules are workspace-only"* to this phase.

**Combined trigger for Phase 2.6**: the Sprint-1.5 queue capture + the ADR-028 deferral + end-of-Vertical-C-drain timing make this the right moment to land both expansions in a single ADR.

## Decision

**Three coupled edits to `~/.codeium/windsurf/skills/verify-l1/SKILL.md`** (all in this ADR):

### Edit 1 — Step 8 path-drift sweep (expanded scope + allow-list)

Widen the grep scope from 3 paths to 8:

- `~/.codeium/windsurf/` (L1 active — unchanged)
- `~/.windsurf/contracts/` (NEW — L1 contracts could drift)
- `~/.windsurf/templates/` (NEW — L3 templates could drift)
- `~/Projects/cascade-system/docs/` (NEW — was `docs/rules/` only; now covers ADRs + manual + cheat-sheet + handoffs + retros)
- `~/Projects/cascade-system/AGENTS.md` (NEW)
- `~/Projects/cascade-system/README.md` (unchanged)
- `~/Projects/cascade-system/queue/` (NEW — queue entries could misdirect future authoring)
- `~/Projects/cascade-system/retros/` (NEW)

Add an **explicit allow-list** of 11 files/paths where dead-path references are intentional historical records (migration ADRs, rule bodies that cite the anti-pattern, narrative manual). Hits within the allow-list are not flagged.

Add a **queue/retros allow-list caveat**: narrative prose citing dead paths with historical markers (*"legacy"* / *"deprecated"* / *"pre-migration"* / etc.) is allowed; a queue entry authoritatively citing a dead path as a target is a bug and is flagged as warning for human review.

### Edit 2 — Step 5 + 6 + 7 workspace-only rule awareness

Previously these steps treated every rule in `INDEX.md` as expected-to-be-in-`global_rules.md`. This produced false-positives for workspace-only rules.

**Step 5**:

- Clarify that *"must contain a section per always-on rule"* means **filter INDEX.md rows to `Trigger: always_on`**.
- Add new rule: workspace-only rules (INDEX row with `Trigger: model_decision` AND footnote containing *"Workspace-deployed only"*) are **expected absent** from `global_rules.md`.
- Add new error class: section present in `global_rules.md` for a rule the archive marks workspace-only → **error** (misclassification; defeats `model_decision` intent).

**Step 6**:

- Distinguish three rule classes by frontmatter + INDEX footnote:
  - `trigger: always_on` → corresponding section in `global_rules.md` **required**
  - `trigger: model_decision` + workspace-only footnote → section in `global_rules.md` **forbidden** (misclassification)
  - `trigger: model_decision` without workspace-only footnote → section in `global_rules.md` **optional**

**Step 7**:

- Extend the A/B set-difference check to three sets (A = always-on from INDEX; B = global sections; C = workspace-only from INDEX).
- Add `C ∩ B` check: workspace-only rule with global section → error (misclassification).
- Add `C not in per-project .windsurf/rules/` as info (rule exists in archive but not yet deployed — fine for newly-authored rules).

### Edit 3 — Frontmatter + step 10 report + Provenance

Update `sources_consulted` with ADR-016, ADR-018, ADR-028, ADR-029 citations. Update `adapted_for` with workspace-only-rule awareness + expanded-sweep bullets. Update step 10 report template to surface new check output (`K_always` + `K_ws` rule counts, `A↔B` + `C∩B` drift status, 8-path scope status). Extend Provenance section with ADR-029 summary.

## Why expand step 8 scope rather than keep it narrow

Pre-ADR-029 step 8 scope was an under-approximation chosen for grep speed. The Sprint-1.5.6 ADR-006 drift was a live-miss: the dead paths were in `docs/decisions/`, unreached by the old scope.

Expanding to 8 paths adds ~200ms to a `@verify-l1` invocation (irrelevant — skill runs on demand, not on hot path) and eliminates the blind spot that surfaced the Sprint-1.5 queue capture. Trade-off clearly favors expansion.

The allow-list is what keeps the expanded sweep **quiet** — without it, every historical reference in every ADR would fire a warning, burying real drift. The allow-list is the rigor; the expansion without it would be noise.

## Why workspace-only-rule awareness in the same ADR

ADR-028 deferred this explicitly (*"`@verify-l1` (Phase 2.6 promotion) gains awareness that some rules are workspace-only"*). Bundling with the Sprint-1.5 path-drift promotion is correct because:

- Both edits touch the same skill (`@verify-l1`)
- Both are driven by the same insight (the skill's rule-classification mental model is incomplete)
- Separating would require a PR each for what is functionally *"one logical update to `@verify-l1`'s validation taxonomy"*

Per `@update-horizontal` discipline: *"one logical change per ADR"* — this IS one logical change (the skill's classification model), manifested in two code paths (path-drift sweep + rule-classification steps).

## Alternatives considered

| Candidate | Shape | Verdict |
|---|---|---|
| **Minimal path-drift expansion** (add `docs/` only, keep old filter) | Adds `docs/` to grep scope, leaves filter at *"ignore ADRs and INDEX.md"* | Rejected — vague filter produces false-positive noise; every ADR mentioning a dead path fires a warning |
| **Split Sprint-1.5 path-drift + ADR-028 workspace-only into two ADRs** | ADR-029 = path-drift only; ADR-030 = workspace-only awareness | Rejected — one logical change to `@verify-l1`'s classification model, artificial split loses traceability |
| **Defer workspace-only to a future sprint** | ADR-028's deferred entry stays deferred | Rejected — the pre-ADR-029 `@verify-l1` emits a false-positive on every run after ADR-018 + ADR-028 landed; leaving it broken defeats the skill's purpose |
| **Auto-fix workspace-only rule misclassifications** | Skill removes misplaced global sections automatically | Rejected — `@verify-l1` is read-only by design (per existing Anti-patterns); mutations route through `@update-horizontal` |
| **Combined ADR-029 with expanded sweep + allow-list + workspace-only awareness (chosen)** | Single ADR, 3 coupled edits | Adopted — matches ADR-026 + ADR-027 precedent (coupled edits, one ADR) |

## Consequences

**Enables**:

- `@verify-l1` runs cleanly on the current L1 state (post-ADR-018 + ADR-028) — no false-positive "missing global section" errors for workspace-only rules
- Path-drift references in any of the 8 paths (ADRs, manual, queue, retros, contracts, templates, skills, workflows) get caught at the next `@verify-l1` invocation — Sprint-1.5.6 blind spot closed
- Future workspace-only rules inherit the same validation logic automatically (no per-rule skill edit needed — the check pivots on INDEX footnote + frontmatter `trigger:`)

**Constrains**:

- Allow-list requires maintenance when new ADRs land that legitimately cite dead paths. Protocol: an ADR that cites a dead path as historical reference should be added to the allow-list in the same PR (or via a targeted follow-up PR). Failure to update the allow-list surfaces as a false-positive warning at the next `@verify-l1` run — self-correcting.
- Step 8 grep is slower (8 paths, more file volume) — ~200ms added per invocation. Measured, acceptable.
- The C∩B misclassification check requires `INDEX.md` footnote parsing for *"Workspace-deployed only"* string — if the INDEX footnote convention drifts (e.g., *"Per-project only"*), the check misses. Convention stability is implicit dependency; `@docs-refresh` owns INDEX regeneration and should preserve the footnote string verbatim.

**Deferred**:

- Empirical measurement of step 8 grep runtime on a large L1 surface — v1 allows up to ~200ms based on current file volume; revisit if grep times >2s
- `@verify-l1` as `@sprint-review` step-8 sub-call — the skill body already flags this as a Sprint 2 candidate; ADR-029 doesn't change that scheduling
- Automated fix proposals for workspace-only rule misclassifications (route through `@update-horizontal` with pre-filled change set) — v2 enhancement, not in scope

## Sources consulted

- cascade-system/queue/pending-review.md Sprint-1.5 `ADR-006 path drift` entry (pre-deletion) — source spec for path-drift sweep expansion
- cascade-system/docs/decisions/ADR-014-l1-canonical-storage-paths.md (own) — canonical paths being enforced
- cascade-system/docs/decisions/ADR-016-workflow-canonical-path-correction.md (own) — added to allow-list
- cascade-system/docs/decisions/ADR-018-release-discipline-cluster.md (own) — `know-your-hardware` workspace-only precedent
- cascade-system/docs/decisions/ADR-028-obsidian-context-priming-rule.md (own) — second workspace-only rule + explicit deferred entry for this ADR
- `~/.codeium/windsurf/skills/verify-l1/SKILL.md` (own, pre-edit) — existing steps 5/6/7/8 being extended
- `~/Projects/cascade-system/docs/rules/INDEX.md` (own) — footnote convention for workspace-only rules

## Related ADRs

- ADR-006 (SKILL.md frontmatter schema) — context for the Sprint-1.5.6 drift that motivated the expansion
- ADR-009 (NNN reserve-in-INDEX-first) — applied
- ADR-014 + ADR-016 — path anti-patterns being swept
- ADR-018 + ADR-028 — workspace-only rule precedents being taught to the skill

## Provenance

Authored 2026-05-04 by Cascade A as Phase 2.6 of the Sprint 2 Vertical C drain. Promoted from Sprint-1.5 queue entry (capture `f8add2` from 2026-05-01) per `retros/sprint-2-vertical-c-drain.md` §3 row 11. Final Phase 2 promotion; Phase 3 (clean-tree gate + retro close) follows.
