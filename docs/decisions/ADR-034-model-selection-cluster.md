# ADR-034: Model-selection cluster — advisor rule + handoff skill pair + cheat-sheet entry

**Status**: Accepted
**Date**: 2026-05-08
**Plan**: `~/.windsurf/plans/sprint-review-cascade-d-mid-vertical-691e32.md` (Cascade D mid-vertical capture)
**Source retro**: `~/Projects/cascade-system/retros/cascade-d-mid-vertical-capture.md` §triage row "Model-switching advisory + handoff workflow"
**Source queue entry**: `2026-05-08 — cascade-system — Cascade D — Model-switching advisory + handoff workflow` (`queue/pending-review.md` line 273 pre-drain) — **Severity: Medium**
**Related**: ADR-017 (`@propose-extension` intake — required path for new skill / rule authoring); ADR-018 (release-discipline cluster — `@release-manager` PR-close as natural session boundary, complements model-switch boundary); ADR-020 (`@kickoff` — pickup pattern for fresh sessions); ADR-033 (token-economy discipline — fresh-session-per-milestone composes with model-switch-per-phase-type)
**Issue**: tracked in retro; no separate GitHub issue (cluster-scope; concrete implementation flows through `@propose-extension` → `@write-skill` per artifact)

## Context

User flagged at M2D.4 closing exchange (verbatim):

> *"also changing the model in cascade is not easy so we need to find a solution to help us changing the model effeciently. opus 4.7 for thinking planing deciding. sonnet 4.6 1m for coding."*

The constraint: Windsurf model selection is **per-session** (set via UI before the session opens), **not per-turn**. There is no in-session model switch; the user must spawn a new Cascade session in the target model and re-prime it.

This produces a recurring friction pattern across verticals:

| Phase type | Optimal model | Observed pattern |
|---|---|---|
| Decision-heavy (ADR drafts, brainstorm walks, architectural choices, retro authoring) | Opus 4.7 (better reasoning, more concise structured thinking) | Used for M2D.0–M2D.4 (this conversation included) |
| Code-heavy (tooling installs, pilot data loops, script authoring, scaffold edits, refactors) | Sonnet 4.6 1M (faster code generation, longer context for repo-wide passes) | Will be optimal for M2D.5+ (HORUS implementation phases) |

**Same pattern will fire on every vertical**:

- Decision phase (PRD authoring, brainstorm walk, ADR drafting) — Opus
- Execute phase (`/run-phase implement`, `/run-phase experiment`, code refactors, test authoring) — Sonnet
- Documentation phase (writeup, handoff authoring) — depending on size; mixed

Without infrastructure, the user has to:

1. Manually decide "this is now code-heavy work"
2. Stop the current session
3. Open a new Cascade in the right model
4. Re-prime it with context (read AGENTS.md, current handoff, recent commits, etc.)

Step 4 is where token cost compounds — every phase-type transition pays full re-priming overhead.

**Composability with ADR-033**: ADR-033's "fresh session per milestone" boundary aligns naturally with phase-type transitions (since `phases.yaml` typically alternates decision-phases with execute-phases). A single "session boundary" event can serve both purposes.

## Decision

**Author three coupled artifacts as a coherent cluster**, all routed through `@propose-extension` → `@write-skill` (skills) and `@update-horizontal` (rule + cheat-sheet edit).

### Artifact 1 — `model-selection-advisor` workspace rule

**Type**: workspace rule (NOT global), `model_decision`-activated. Mirrors `know-your-hardware` (ADR-018) and `obsidian-context-priming` (ADR-028) precedents — workspace-only `model_decision` rules with INDEX-marked workspace-only footnote.

**Path**: `~/Projects/cascade-system/docs/rules/model-selection-advisor.md` (long-form archive) + INDEX entry marked workspace-only. Per ADR-029 step 5/6/7 awareness, `@verify-l1` won't expect a `global_rules.md` section for this rule.

**Activation**: fires when conversation enters phase-typed territory:

- **Code-heavy signal** (suggests Sonnet): multiple consecutive `write_to_file` / `edit` / `multi_edit` calls in a row; OR a `phases.yaml` step explicitly tagged `phase_type: execute`; OR Cascade about to invoke `@tdd` / `@run-experiment` / `@implement`-class skills.
- **Decision-heavy signal** (suggests Opus): about to invoke `@grill-me` / `@to-prd` / ADR drafting / retro authoring; OR `phase_type: decide` in `phases.yaml`.

**Action**: emit a one-line advisory ("⚠ This work would benefit from <Sonnet 4.6 1M / Opus 4.7> — consider invoking `@handoff-to-<coding|thinking>-session`") then continue. **Never auto-switches.** **Never blocks.** User decides.

**Suppression**: per-session — if user declines once, the rule stops emitting for that session.

### Artifact 2 — `@handoff-to-coding-session` + `@handoff-to-thinking-session` skill pair

**Type**: L1 skills, paired. Symmetric in structure, mirror-image in target model.

**Path**: `~/.codeium/windsurf/skills/handoff-to-coding-session/SKILL.md` + `~/.codeium/windsurf/skills/handoff-to-thinking-session/SKILL.md` (per ADR-014 canonical L1 paths).

**Procedure (both skills)**:

1. **Identify the handoff scope** — current task / phase / milestone the user wants to continue in the target session.
2. **Compile context bundle**:
   - Decisions made so far in this session (referenced ADRs, locked-in choices)
   - Task list (what the new session should pick up)
   - File paths it should read first (handoff doc, current `phases.yaml` step, relevant ADRs)
   - Optional: `obsidian` CLI primer commands if vault is in scope (per ADR-028)
3. **Write to** `~/.windsurf/handoffs/<session-id>-<target-model>.md`. ID convention: `<vertical-or-project-slug>-<YYYYMMDDHHMM>-<phase-type>`. E.g., `horus-202605091430-coding.md`.
4. **Print the prompt for the user**:
   ```
   Handoff written: ~/.windsurf/handoffs/<file>.md
   Open a new Cascade in <Sonnet 4.6 1M | Opus 4.7> with first read:
       <handoff-file-absolute-path>
   ```
5. **Exit current session** (or hand control back to user — Cascade does not close itself).

**Anti-patterns**:

- Bundle-handoff-with-task-execution: the skill writes the handoff and exits; it does not start the work the new session should pick up.
- Handoff-without-target-model: the file name encodes target-model; if the user doesn't specify, prompt once for it.

**Pairs with**: `@kickoff` (which is the consumer-side pickup pattern; the new session may invoke `@kickoff <handoff-file>` to orient).

### Artifact 3 — `cascade-system/docs/cheat-sheet.md` "When to use which model" entry

Add a small table to the cheat-sheet (likely under existing §"Invocation quick-reference" or as a new §"Session ergonomics"):

| Phase type | Preferred model | Pickup pattern |
|---|---|---|
| Decision-heavy (PRD, brainstorm, ADR, retro) | Opus 4.7 | `@handoff-to-thinking-session` → fresh Cascade → `@kickoff <file>` |
| Code-heavy (implement, experiment, refactor, scaffold edit) | Sonnet 4.6 1M | `@handoff-to-coding-session` → fresh Cascade → `@kickoff <file>` |
| Mixed / routine (queue capture, docs edit, small PR) | Either; stay in current session if under 50k token threshold (ADR-033) | n/a |

## Consequences

**Positive**:

- Codifies a discipline the user is already practising informally; lowers cognitive load (no "should I switch?" question per turn — the rule advises).
- Reduces re-priming cost (handoff bundle replaces ad-hoc copy/paste of context into a new session).
- Composes with ADR-033 (fresh-session-per-milestone) and ADR-020 (`@kickoff` pickup) — single mental model for session boundaries.
- Workspace-rule scope (not global) avoids `global_rules.md` size pressure (currently 5972/6000 chars per Vertical C2 retro §6).

**Negative / risks**:

- **Three artifacts is a cluster** — `no-quantity-over-shape` discipline says don't author 3 things to solve 1 problem unless each is load-bearing. Mitigation: each artifact serves a distinct role (rule = signal; skills = mechanism; cheat-sheet = discoverability) and they compose. If one artifact is dropped, the cluster degrades but remains functional (e.g., skip the cheat-sheet entry — the skills are still discoverable via `@skills:` browser).
- **Single data point** (M2D.4 friction) for cluster-scope promotion. Mitigation: the user explicitly requested this in the queue entry; further data points would only confirm a need already articulated. The cluster is small enough to land on one data point with re-evaluation triggers per artifact (see "Re-evaluation triggers" below).
- **Advisor rule false-positive risk** — over-eager firing during transient code-edit clusters that aren't really phase-type transitions. Mitigation: per-session suppression after one decline; advisory-only (never blocks).
- **Skill pair line-count budget** — based on M2B.4 calibration (skill body line-count queue entry, line 207), composing skills typically run 130–140 lines. Two skills × 130 lines ≈ 260 lines of new L1 skill surface. Acceptable.

**Neutral**:

- `@update-horizontal` Propagate table will need new rows for "new L1 skill" pattern (touches the `@write-skill` step 9 inaccuracy queue entry — out-of-scope for this drain but pairs naturally).

## Re-evaluation triggers (per artifact)

| Artifact | Re-evaluate when |
|---|---|
| `model-selection-advisor` rule | After 2nd vertical close shows whether the rule fires often enough to justify global-rule promotion (vs. staying workspace-only) |
| `@handoff-to-coding-session` + thinking-session skills | After 5+ invocations across verticals — calibrate the handoff-bundle template based on what users actually need |
| Cheat-sheet "When to use which model" entry | When Windsurf model lineup changes (new model release supersedes Opus 4.7 / Sonnet 4.6 1M) |

## Alternatives considered

### Alt 1: Just author the cheat-sheet entry; skip the rule and skills

Lightest path; documentation-only.

**Rejected** — defeats the user's explicit ask. Documentation alone won't reduce the per-transition re-priming cost; that requires the handoff skills' context-bundle mechanism. The rule is the trigger that makes the skills discoverable in-session.

### Alt 2: Merge the two skills into one `@handoff-to-fresh-session --target-model <m>`

Single skill with target-model as a flag.

**Rejected** — discoverability. Two distinct skills appear in `@skills:` browser; one parameterized skill is hidden behind a flag. The user-explicit ask names both directions ("opus for thinking, sonnet for coding") — naming both skills mirrors that mental model.

### Alt 3: Promote the rule to global_rules.md (always-on)

Make the advisor fire across all projects always.

**Rejected** — `global_rules.md` is at 5972/6000 chars; adding a section costs precious budget. Workspace-only `model_decision` activation is the same pattern used by `know-your-hardware` and `obsidian-context-priming` — cheaper, more contextual, won't fire in projects where model-switching isn't a concern (e.g., short single-session prototypes).

### Alt 4: Defer entirely; collect more data points first

Wait until 2–3 verticals have produced explicit model-switch friction before authoring anything.

**Rejected** — the user has explicitly requested the cluster. Deferring would be a `be-honest-direct-critical` failure (we have a clear ask + a clear shape; the only uncertainty is calibration, which the per-artifact re-evaluation triggers handle).

## Apply via

`@propose-extension` → multi-target dispatch:

- Artifact 1 (rule): `@update-horizontal` adds the rule file at `docs/rules/` + INDEX entry
- Artifact 2 (skill pair): `@write-skill` per skill (two invocations); `@verify-l1` post-author
- Artifact 3 (cheat-sheet): direct edit (no skill needed)

Sequencing: artifacts 2 + 3 can land in any order; artifact 1 (rule) should land last so its emitted advisory references existing skill names. **All three** ship before declaring ADR-034 `Status: Accepted`.

## Provenance

Sources consulted:

- `queue/pending-review.md:273` (model-switching advisory queue entry, 2026-05-08)
- M2D.4 closing exchange chat trajectory (user verbatim ask)
- ADR-018 §"`know-your-hardware` workspace-only" (workspace-rule precedent)
- ADR-028 (`obsidian-context-priming` rule — second workspace-only `model_decision` precedent)
- ADR-029 §Edit 2 (`@verify-l1` workspace-only-rule awareness — already-implemented; no new infra needed for this ADR)
- ADR-020 (`@kickoff` skill — pickup pattern for handoff consumers)
- ADR-033 (token-economy discipline — composable session-boundary signal)
- `~/Projects/cascade-system/docs/rules/no-quantity-over-shape.md` (cluster-justification load-bearing)
- `~/.codeium/windsurf/skills/release-manager/SKILL.md` (sibling pattern: skill that hands off to helper workflows; informs handoff-skill structure)

Adapted for: cascade-system meta-repo conventions (workspace rule + ADR-NNN doc + cheat-sheet pattern), Windsurf per-session model constraint (no in-session switch), our L1 / L2 / L3 layering.

## Applied via

Applied 2026-05-09 in branch `feat/adr-034-model-selection-cluster` (single PR, milestone-gated full ledger; last of 4 ADRs from Cascade D mid-vertical capture retro: ADR-032 #97 ✅, ADR-033 #98 ✅, ADR-035 #99 ✅, ADR-034 this PR).

**Versioned (this repo)**:

- `docs/cheat-sheet.md` — §8 "Session ergonomics" gains new "When to use which model" subsection (sibling to Token-economy practices from ADR-033); §3 stale rule count fix (17 → 19; 1 workspace-only → 3 workspace-only listing all three).
- `docs/rules/model-selection-advisor.md` — new long-form rule (`trigger: model_decision`, workspace-only per ADR-018 + ADR-028 precedent).
- `docs/rules/INDEX.md` — new row for `model-selection-advisor.md` with workspace-only footnote.
- `docs/decisions/INDEX.md` — ADR-034 row flipped to `Accepted` with applied-to footnote.
- `docs/decisions/ADR-034-*.md` (this file) — `Status: Proposed → Accepted`; this section appended.

**L1 active surface (unversioned per ADR-014)**:

- `~/.codeium/windsurf/skills/handoff-to-coding-session/SKILL.md` — new skill (target = Sonnet 4.6 1M; 138 lines).
- `~/.codeium/windsurf/skills/handoff-to-thinking-session/SKILL.md` — new mirror skill (target = Opus 4.7; 139 lines).
- `~/.windsurf/handoffs/` — new directory with `README.md` stub explaining the filename convention + retention policy.

**Not touched**: `~/.codeium/windsurf/memories/global_rules.md` (5943/6000 chars; workspace-only rule has no concise entry per ADR-018 + ADR-028 precedent and the explicit Alternative 3 rejection above).

**Validation**:

- `@verify-l1` audit clean (canonical paths intact; no deprecated path leaks; new skills' frontmatter valid; new rule absent from `global_rules.md` as expected per ADR-029 step 5/6/7 awareness).
- Sequencing followed: skills + cheat-sheet first, rule last (rule body references skill names that exist on disk).
- 4-of-4 Cascade D mid-vertical-capture ADRs now `Accepted`.
