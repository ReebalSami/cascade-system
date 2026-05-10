---
trigger: model_decision
description: Emits a one-line advisory when phase-typed signals suggest the active session would benefit from a different model — Sonnet 4.6 1M for code-heavy work (multiple consecutive `write_to_file`/`edit`/`multi_edit` calls; `phase_type: execute` in `phases.yaml`; about to invoke `@tdd`/`@run-experiment`/`@implement`-class skills) or Opus 4.7 for decision-heavy work (about to invoke `@grill-me`/`@to-prd`/ADR drafting/retro authoring; `phase_type: decide` in `phases.yaml`). Advisory points to `@handoff-to-coding-session` / `@handoff-to-thinking-session` skill pair. Never auto-switches; never blocks. Per-session suppression after one decline. Workspace-deployed only (no entry in `global_rules.md`) per `know-your-hardware` + `obsidian-context-priming` precedent.
sources_consulted:
  - cascade-system/docs/decisions/ADR-034-model-selection-cluster.md (own) — the ADR this rule implements (Artifact 1 of the cluster)
  - cascade-system/docs/decisions/ADR-018-release-discipline-cluster.md (own) — `know-your-hardware` workspace-only precedent for `model_decision`-activated rules (per the ADR-018 brainstorm drift correction)
  - cascade-system/docs/decisions/ADR-028-obsidian-context-priming-rule.md (own) — second workspace-only `model_decision` precedent; pattern this rule mirrors structurally
  - cascade-system/docs/decisions/ADR-029-verify-l1-path-drift-sweep.md (own) — `@verify-l1` workspace-only-rule awareness in steps 5/6/7 (this rule benefits from already-implemented detection)
  - cascade-system/docs/decisions/ADR-033-token-economy-discipline.md (own) — fresh-session-per-milestone composes with model-switch-per-phase-type; both signal "session boundary"
  - cascade-system/docs/decisions/ADR-014-l1-canonical-storage-paths.md (own) — `global_rules.md` 6000-char cap rationale for workspace-only deployment
  - cascade-system/docs/rules/know-your-hardware.md (own) — style template for workspace-only `model_decision` rule (long-form archive at `docs/rules/` + deployment via `/start-project` step 6a)
  - cascade-system/docs/rules/obsidian-context-priming.md (own) — closer style template (watcher-archetype, single-line surface, suppression pattern)
  - cascade-system/docs/rules/plan-drift-watcher.md (own) — watcher-archetype style: inline surface + suppression pattern
  - cascade-system/docs/rules/sprint-review-prompt.md (own) — watcher-archetype style: trigger-on-state-transition + don't-auto-invoke pattern
  - cascade-system/retros/cascade-d-mid-vertical-capture.md (own) — §Learning 6 source observation (M2D.4 user verbatim ask: "opus 4.7 for thinking planing deciding. sonnet 4.6 1m for coding")
  - ~/.codeium/windsurf/skills/handoff-to-coding-session/SKILL.md (own) — paired skill the advisory points to (code-heavy direction)
  - ~/.codeium/windsurf/skills/handoff-to-thinking-session/SKILL.md (own) — paired skill the advisory points to (decision-heavy direction)
adapted_for:
  - Windsurf workspace-only `model_decision` activation (per ADR-018 + ADR-028 — Windsurf treats `global_rules.md` entries as always-on regardless of frontmatter; only workspace rules support true `model_decision` semantics)
  - Pairs with `@handoff-to-coding-session` + `@handoff-to-thinking-session` skill pair (ADR-034 Artifact 2) — the advisory references both skill names directly
  - Per-session suppression after one decline (matches `plan-drift-watcher` + `sprint-review-prompt` patterns)
  - Advisory-only — never auto-switches, never blocks; user always decides
  - Composes with `bidirectional-learning-pipe`: false-positive observations flow to queue for re-evaluation per ADR-034 §Re-evaluation triggers
---

# Model-selection advisor

> **CONSTRAINT**: Windsurf model selection is **per-session** (set via UI before the session opens), **not per-turn**. There is no in-session model switch; switching means spawning a new Cascade session in the target model and re-priming it. This rule surfaces when the current model and the in-flight phase type are mismatched. The user decides whether to switch.

## What this rule does + does NOT do

| Does | Does NOT do |
|---|---|
| Emit a single-line advisory when phase-typed signals fire | Auto-switch the model |
| Reference `@handoff-to-coding-session` / `@handoff-to-thinking-session` by name | Block the user from continuing in the current model |
| Suppress after one decline (per session) | Persist suppression across sessions |
| Compose with ADR-033 fresh-session-per-milestone signal | Replace the user's judgment about whether to switch |

## Activation triggers

The rule fires when the conversation enters phase-typed territory:

### Code-heavy signals (suggest Sonnet 4.6 1M)

- Multiple consecutive `write_to_file` / `edit` / `multi_edit` tool calls in a row (e.g., 3+ within a 5-turn window)
- A `phases.yaml` step explicitly tagged `phase_type: execute` (per `phase-taxonomy` contract)
- Cascade about to invoke `@tdd` / `@run-experiment` / `@implement`-class skills
- User phrasing: *"let's implement"*, *"now I'll write the code"*, *"refactor this"*, *"scaffold the X"*

### Decision-heavy signals (suggest Opus 4.7)

- About to invoke `@grill-me` / `@to-prd` / ADR drafting / `@sprint-review` retro authoring
- A `phases.yaml` step explicitly tagged `phase_type: decide` (per `phase-taxonomy` contract)
- User phrasing: *"let's brainstorm"*, *"draft an ADR"*, *"think through the trade-offs"*, *"close the milestone"*

If neither set of signals fires: silent no-op.

## Behavior when fired

Single-line inline surface, watcher-archetype style (matches `plan-drift-watcher` + `sprint-review-prompt`):

> ⚠ This work would benefit from **<Sonnet 4.6 1M | Opus 4.7>** — consider invoking `@handoff-to-<coding|thinking>-session` to spawn a fresh Cascade in the right model. Say "skip model advisor" to disable for this session.

Then continue the current task. The advisory is non-blocking; Cascade does NOT pause work waiting for an answer.

The user has three responses:

1. **Accept** — invoke the suggested handoff skill; current session writes the bundle and exits
2. **Defer** — continue in the current model; advisor remains active and may fire again on a new signal cluster
3. **Decline / suppress** — *"skip model advisor"* / *"no model switch"* / *"stay in this model"* → suppress for the rest of this session

## Suppression

Per-session only. Reset on next session (matches `plan-drift-watcher` + `sprint-review-prompt`). Suppression persistence across sessions is intentionally NOT supported — phase-type expectations may change between sessions.

## Composition with other rules

- **`bidirectional-learning-pipe`**: false-positive advisory firings (rule fired but user judged the session was actually mixed-mode) flow into `queue/pending-review.md` per ADR-034 §Re-evaluation triggers. The "after 2nd vertical close" trigger calibrates whether the rule fires often enough to justify global-rule promotion.
- **`no-half-knowledge`**: rule reads `phases.yaml` (full-file) when checking `phase_type` — does NOT partial-read.
- **`no-quantity-over-shape`**: signal thresholds described as shape (consecutive code-edit cluster), not as fixed counts — "3+ within 5 turns" is illustrative, not a hard threshold.
- **`be-honest-direct-critical`**: when fired, surface the signal that triggered the advisory (e.g., *"Triggered by 4 consecutive `multi_edit` calls — code-heavy pattern"*) so the user can judge whether the signal was noise.
- **ADR-033 token-economy fresh-session-per-milestone**: `@release-manager` PR-close + phase-type transition is the canonical session-boundary signal. Both this rule and ADR-033 may fire at the same boundary; their advisories compose (one mention, not two).

## Workspace-only activation

Per ADR-018 + ADR-028 precedent: `model_decision` activation requires workspace-scoped rules (Windsurf treats entries in `global_rules.md` as always-on regardless of frontmatter annotation). To preserve the *"fires only when phase-typed signals warrant"* semantic, this rule lives at `<project>/.windsurf/rules/model-selection-advisor.md` in every project that cares about model-switching ergonomics.

Additional rationale beyond the always-on / model_decision constraint:

- **`global_rules.md` budget headroom**: at 5943 / 6000 chars (as of ADR-034 apply), every char of headroom is precious for genuinely cross-cutting always-on rules; this advisor is contextually scoped (only fires on phase-typed transitions in projects with phases.yaml).
- **Project-relevance scoping**: short single-session prototypes (no `phases.yaml`, no multi-phase work) don't need this advisor. Workspace-only deployment means it doesn't fire where it isn't wanted.

Deployment:

- **New projects**: `/start-project` step 6a `cp ~/Projects/cascade-system/docs/rules/*.md <parent>/<name>/.windsurf/rules/` picks this file up automatically
- **Existing projects** that want the advisor: manual copy required to opt in (matches `obsidian-context-priming` deployment model)
- **cascade-system itself**: opt-in for the meta-repo when phase-typed work surfaces; not strictly needed for capture / queue-drain sessions

## Re-evaluation triggers

Per ADR-034 §Re-evaluation triggers:

- After 2nd vertical close shows whether the rule fires often enough to justify global-rule promotion (vs. staying workspace-only). If false-positive rate is low and firing frequency is high, reconsider — but `global_rules.md` budget pressure makes promotion costly.
- If false-positive rate is high (>50% declines): tighten the signal definitions; surface to next `@sprint-review`.

## Provenance

ADR ref: `~/Projects/cascade-system/docs/decisions/ADR-034-model-selection-cluster.md` (Artifact 1). Source retro: `~/Projects/cascade-system/retros/cascade-d-mid-vertical-capture.md` §Learning 6 (M2D.4 user verbatim ask: *"also changing the model in cascade is not easy so we need to find a solution to help us changing the model effeciently. opus 4.7 for thinking planing deciding. sonnet 4.6 1m for coding."*). Workspace-only-deploy precedent: ADR-018 (`know-your-hardware`) + ADR-028 (`obsidian-context-priming`). `@verify-l1` workspace-only-rule awareness: ADR-029 steps 5/6/7.
