# ADR-019: `@begin` as front-door entry skill for new projects

**Status**: Accepted
**Date**: 2026-05-02
**Source**: Sprint 1.5 audit + plan `~/.windsurf/plans/sprint-1-5-onboard-ramp-f8add2.md` §2 D1, §2 D2, §3 1.5.1; conversation `f8add2`.

## Context

The cascade-system has all the right primitives for starting a new project — `@grill-me` for refining ideas, `/add-project-type` for new L3 templates, `/start-project` for bootstrapping, `/run-phase` for dispatching phases, the L3 template library at `~/.windsurf/templates/`. But there is **no orchestrating front-door skill**. To start a project from a brainstorm note, the user has to:

1. Know which workflow chain to invoke (`/add-project-type` if the stack is new, otherwise straight to `/start-project`).
2. Know which skills to call inside each step (`@grill-me` for refining the idea before bootstrap; `@grill-me` again inside `/add-project-type` for the phase set).
3. Hand-author a 50–200 line kickoff plan to feed into a fresh Cascade.

The `queue/pending-review.md` "vertical-spawn bootstrap gap" entry already flagged this for `@sprint-review` as the **vertical-spawn** case. Sprint 1.5's audit surfaced that the same orchestration gap exists for the **new-idea** case — and that the two cases want different shapes (idea path is grill-heavy; vertical-pickup is read-handoff-heavy).

The `parked-items-brainstorm.md` (commit `fa48ea2`) and Sprint 2 verticals B/C/D demonstrate the kickoff-plan-as-workaround pattern empirically: each vertical's plan at `~/.windsurf/plans/<vertical>-kickoff-*.md` recreates the same shape (read-list, mandatory channels, hard gate, first action) by hand. That repetition is the empirical evidence the orchestrator should be a first-class skill, not a hand-authored artifact per spawn.

## Decision

Author `@begin` as an L1 skill at `~/.codeium/windsurf/skills/begin/SKILL.md` with `activation: auto`. The skill:

1. Auto-activates on user phrases like *"I have a new idea"*, *"start a new project"*, *"let's build X"*, or when the user passes a path to a brainstorm/idea note.
2. **Hard-gates** if the cwd is already inside an existing project repo with a `.windsurf/phases.yaml` — that's `@kickoff` or `/run-phase` territory, not `@begin`.
3. Reads the idea note (or solicits a description) and dispatches to `@grill-me` to refine scope, constraints, primary stack, target users, and success criteria.
4. Enumerates `~/.windsurf/templates/<type>/` (excluding `_shared/`) and asks the user to pick or recommends the closest match.
5. If no L3 type covers the stack: dispatches to `/add-project-type` with the gathered context, then to `/start-project <name> <new-type>`.
6. If a type covers: dispatches directly to `/start-project <name> <type>`.
7. After bootstrap: dispatches to `/run-phase brainstorm` (the project's own brainstorm phase, which captures the refined idea inside the now-bootstrapped project at `docs/prompts/stages/02-brainstorm.md`).

`@begin` orchestrates; it does **not** re-implement any of the dispatched primitives. The skill body stays ≤150 lines per the ADR-006 + mattpocock heuristic.

### Two-skill split: `@begin` for ideas, `@kickoff` for verticals

The new-idea path and the vertical-pickup path are separate skills (paired ADRs: this one and ADR-020 for `@kickoff`) per plan §2 D1. The two paths exercise different sub-flows:

- **Idea path** (this ADR): `@grill-me` heavy + stack-decision + `/add-project-type` (conditional) + `/start-project` + `/run-phase brainstorm`.
- **Vertical path** (ADR-020): read handoff doc + read parent plan + read cited ADRs + lifecycle-position detection + `@to-issues` (if missing) + `/run-phase <last-incomplete-phase>` or first-action prompt.

One skill body covering both becomes confusing prose. Two skills, each with a single purpose, are clearer to author and easier for Cascade to dispatch via description-match.

### No "resume" mode

The audit considered a third mode — *"resume work in this directory"* — and rejected it (plan §2 D1 anti-decision). `AGENTS.md` auto-loads when a workspace opens, L2 rules auto-load, and `/run-phase` (no arg) lists current phases with artifact status. Those primitives plus a one-line user question (*"what's in flight in this repo?"*) cover resume cleanly without a third skill.

### Skill, not workflow

`@begin` is a **skill** (per ADR-015):

- **Auto-activation** — users say *"I have a new idea"* and Cascade routes them automatically; a workflow would force users to remember `/begin`.
- **Judgment-heavy** — choosing between `/add-project-type` first or straight `/start-project`, recommending an L3 stack from the enumeration, and deciding when the brainstorm is refined enough to bootstrap are all judgment calls.
- **Composable** — `@begin` dispatches to `@grill-me`, `/add-project-type`, `/start-project`, `/run-phase`. Skills compose with skills + workflows; workflow-to-skill delegation is awkward.

A thin `/begin` workflow wrapper for slash-discoverability is **not** in scope for Sprint 1.5 — the skill is auto-activable, so the wrapper would be redundant. If discoverability via slash menu becomes a felt need post-launch, add the wrapper later.

## Alternatives considered

- **One skill covering all three intents** (idea / vertical-pickup / resume) — rejected per §2 D1. Three branches in one prose body becomes confusing; resume doesn't need a skill at all.
- **Workflow `/begin` instead of skill `@begin`** — rejected per ADR-015. Loses auto-activation on user phrasing.
- **Extend `/start-project` with a `--from-idea` flag** — rejected. Overloads a workflow that has a clear single purpose (consume an L3 template). The orchestration needed before `/start-project` (grill, stack-decide, conditional `/add-project-type`) is qualitatively different from scaffold-and-bootstrap.
- **Author the orchestration inside `@grill-me`** — rejected. `@grill-me` is a Socratic-interview primitive used by many skills (including `@begin`, `/add-project-type`'s phase-set design, `@to-prd`'s open-question pass). Bloating it with project-bootstrap orchestration would couple the wrong concerns.

## Consequences

**Enables:**

- One-line invocation for new-idea project starts: *"@begin <idea-note-path>"* replaces 50–200 line hand-authored kickoff plans.
- Stack-decision is interactive (user picks from enumerated L3 templates) rather than required-up-front (`/start-project` already requires the type as a positional arg).
- The `/add-project-type` → `/start-project` chain is automatically routed when no L3 type covers; users don't have to know the workflow chain themselves.
- `@grill-me` gets invoked at the right point (before stack decision, while the idea is still mutable) by default, instead of accidentally being skipped because the user didn't think to call it.

**Constrains:**

- All future new-project starts are expected to route through `@begin`. Direct `/start-project <name> <type>` invocations are still allowed (the workflow remains independently usable) but signal process drift if used to bypass the grill phase.
- The skill body is bounded by the ADR-006 + mattpocock 150-line heuristic. As the idea→bootstrap surface grows (new L3 templates, new conditional pre-bootstrap steps), the skill must avoid bloat by dispatching to other skills rather than absorbing logic.
- `@begin` and `@kickoff` (ADR-020) form a paired surface. Updates to one often imply review of the other for consistency. `@docs-refresh` regenerates `docs/skills/INDEX.md` (Sprint 1.5.4) automatically when either is modified.

**Empirical-test footnote:** the next project start after Sprint 1.5 closes is the live-fire test. If the user invokes `@begin` and reaches *"I have a new project"* state with one line of input (or one focused dialogue exchange), `@begin` proves itself. If a fresh Cascade reaches for `~/.windsurf/plans/<idea>-kickoff-*.md` instead, that's a regression — capture to queue for next `@sprint-review`.

**Bootstrap note:** `@begin` itself is authored without routing through `@begin` (chicken-and-egg: there is no project to begin; this is meta-system work). Sprint 1.5 authored it directly via this ADR + plan. Future meta-skills face the same bootstrap; convention per ADR-017's footnote remains: meta-skills get their own ADR, then become routable.

## Source

- `~/.windsurf/plans/sprint-1-5-onboard-ramp-f8add2.md` §2 D1 + D2, §3 1.5.1 (this skill's spec)
- `cascade-system/queue/pending-review.md` "Sprint 2 prep — vertical-spawn bootstrap gap" entry (the friction motivating the ADR)
- ADR-014 (canonical L1 skill path: `~/.codeium/windsurf/skills/begin/SKILL.md`)
- ADR-015 (skill-vs-workflow choice rationale; cited in §"Skill, not workflow")
- ADR-017 (`@propose-extension` as intake channel — `@begin` was authored via the meta-skill route described there, in batched form during plan mode)
- ADR-006 (skill frontmatter schema this skill follows; ADR-006 itself was corrected for path drift in Sprint 1.5.6)
- `~/.codeium/windsurf/skills/grill-me/SKILL.md` — primary sub-skill invoked by `@begin`
- `~/.codeium/windsurf/global_workflows/{add-project-type,start-project,run-phase}.md` — sub-workflows dispatched by `@begin`
- ADR-020 (`@kickoff` paired skill) — separates the vertical-pickup path
