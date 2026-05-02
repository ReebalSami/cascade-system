# ADR-020: `@kickoff` as front-door entry skill for vertical Cascade pickups

**Status**: Accepted
**Date**: 2026-05-02
**Source**: Sprint 1.5 audit + plan `~/.windsurf/plans/sprint-1-5-onboard-ramp-f8add2.md` §2 D1, §2 D2, §3 1.5.2; conversation `f8add2`. Pairs with ADR-019 (`@begin`).

## Context

Sprint 2 ships work via vertical Cascades — independent sessions that pick up a slice of work documented in a handoff doc at `cascade-system/docs/handoffs/<cascade-id>-<scope>.md`. Verticals B (`python-ml-uv` template), C (Obsidian), and D (thesis) all follow this pattern: a parent Cascade authors a handoff, the user opens a fresh Cascade session, and the new session orients itself to the work.

Today the orientation step is **a hand-authored kickoff plan** at `~/.windsurf/plans/<vertical>-kickoff-<suffix>.md`. The cascade-c-obsidian kickoff (`~/.windsurf/plans/cascade-c-obsidian-kickoff-4fa159.md`) ran 50–200 lines and even included a "Known drift in the handoff doc" section to patch the parent doc inline. That's an anti-pattern: kickoff plans are workarounds for a missing skill.

The friction has three dimensions:

1. **Authoring cost** — the parent Cascade has to write a long kickoff plan per vertical spawn. Sprint 2 with three verticals = three hand-authored plans. The recurring shape (read-list, mandatory channels, hard-gate, first action) is empirical evidence the orchestrator should be a skill.
2. **Drift cost** — the handoff doc + the kickoff plan + the meta-repo's running ADR set are three sources of truth that drift independently. PR #15 fixed cascade-c-obsidian handoff drift after Sprint 1 hardening; the kickoff plan still patched the drift externally. Routing through a skill that re-reads canonical sources at activation time eliminates the drift window.
3. **Discoverability** — a fresh Cascade reading a handoff doc has no automatic routing into the rest of the meta-system. The kickoff plan was the user's manual workaround. A skill that auto-activates on phrases like *"pick up Cascade C"* or *"@kickoff <handoff-path>"* makes the routing implicit.

ADR-019 covers the new-idea case (`@begin`). This ADR covers the paired vertical-pickup case.

## Decision

Author `@kickoff` as an L1 skill at `~/.codeium/windsurf/skills/kickoff/SKILL.md` with `activation: auto`. The skill:

1. Auto-activates on phrases like *"pick up Cascade B/C/D"*, *"@kickoff <handoff-path>"*, *"read this handoff and orient yourself"*, *"I'm spawning a vertical"*.
2. **Hard-gates** if no handoff doc path is provided. The skill orients on a handoff; without one it has nothing to orient on. Routes user to `@begin` (if they actually meant new-idea) or asks for the path.
3. Reads, in order:
   - The handoff doc at the provided path (typically `cascade-system/docs/handoffs/<cascade-id>-<scope>.md`)
   - The parent plan it references (typically `~/.windsurf/plans/<plan>-<suffix>.md`)
   - Every ADR cited in the handoff (typically ADR-014 through 018; vertical-specific ADRs as cited)
   - `cascade-system/AGENTS.md` and `docs/cheat-sheet.md` for meta-system orientation
4. **Determines lifecycle position** of the vertical. Four cases:
   - **Idea-only** — handoff describes an idea but no project bootstrapped → routes to `@begin` (the parent only had an idea; vertical-pickup is the wrong shape)
   - **Project-typed** — L3 template exists but project not bootstrapped → invokes `/start-project`
   - **Bootstrapped** — project exists; phases.yaml exists; some phases done → invokes `/run-phase <last-incomplete-phase>` or lists status with `/run-phase` (no arg)
   - **Mid-execution** — open issues, open PR, recent commits → orient and ask *"continue at <last stage>?"*
5. Files vertical's milestone issues if missing. Sprint 2 verticals (e.g., `M2C.1`–`M2C.6` for cascade-c-obsidian) typically don't exist on GitHub at handoff time; the parent Cascade documents them in the handoff and leaves filing as the picking-up Cascade's first action. `@kickoff` dispatches to `@to-issues` (or direct `mcp3_issue_write` for handoff-style issue lists) to create them, tag to the appropriate milestone, and add to the Project board.
6. Identifies the first concrete action and asks **one focused question** to start the dialogue. *Not* a bundle. *Not* a recap. One question.

`@kickoff` orchestrates; it does not re-implement `@to-issues`, `/start-project`, `/run-phase`, or any other primitive. Body stays ≤150 lines per ADR-006 + mattpocock heuristic.

### Two-skill split (paired with ADR-019)

Same rationale as ADR-019: idea path is grill-heavy + stack-decision-heavy; vertical path is read-handoff-heavy + lifecycle-position-heavy. One skill body covering both becomes confusing prose. Pairing makes both surfaces clearer to author and easier for Cascade to dispatch via description-match.

### Skill, not workflow

Same rationale as ADR-019: auto-activation on user phrasing, judgment-heavy decisions (which lifecycle case? which sub-skill to dispatch? what's the right first question?), composability with `@to-issues`, `/run-phase`, `@release-manager`. Workflow shape would lose all three.

### Legacy kickoff plans not deleted

Per ADR-011 (record retention — supersession over deletion), the existing `~/.windsurf/plans/<vertical>-kickoff-<suffix>.md` files (e.g., `cascade-c-obsidian-kickoff-4fa159.md`) are **not deleted**. They're superseded as the canonical pattern but preserved as historical record of how vertical-pickup happened before the skill existed. New verticals (Sprint 2 onward) use `@kickoff`; legacy kickoff plans stay for archaeological / debugging value.

## Alternatives considered

- **Single skill covering ideas + verticals (rejected per ADR-019 §"Two-skill split")** — already evaluated and rejected in the paired ADR.
- **Workflow `/kickoff` instead of skill `@kickoff`** — rejected per ADR-015 same reasoning as ADR-019. Loses auto-activation.
- **Extend `/start-project` with a `--from-handoff` flag** — rejected. `/start-project` is bootstrap-only; vertical-pickup includes lifecycle-position detection (might NOT need bootstrap), milestone-filing, ADR-reading, etc. Different shape.
- **Author the orchestration inside `@to-issues`** — rejected. `@to-issues` decomposes a PRD into issues; vertical-pickup pre-dates the issues step. Wrong skill to host.
- **Extend `@release-manager` to do vertical-pickup as well as branch lifecycle** — rejected. `@release-manager` is the *exit* path (ship to main); `@kickoff` is the *entry* path. Different lifecycle stages, different concerns.
- **Auto-detect lifecycle case via heuristics and silently dispatch to `/start-project` or `/run-phase`** — rejected. Auto-dispatch removes user agency on a compounding decision (the lifecycle case determines which milestones get filed and which phases run). Better to make the case explicit + ask one focused question.

## Consequences

**Enables:**

- One-line invocation for vertical pickup: *"@kickoff cascade-system/docs/handoffs/cascade-c-obsidian.md"* replaces 50–200 line hand-authored kickoff plans.
- Drift between handoff doc / parent plan / ADRs is mitigated — `@kickoff` re-reads canonical sources at activation time rather than relying on a pre-baked summary.
- Lifecycle-position detection prevents the wrong-shape pickup (e.g., trying to `/run-phase` on a not-yet-bootstrapped vertical).
- Milestone filing is automated — Sprint 2 verticals' `M2C.1`–`M2C.6` (and equivalent) get created on first `@kickoff` invocation, no manual upfront filing needed.

**Constrains:**

- All future vertical-pickups are expected to route through `@kickoff`. Direct `cd` + ad-hoc `@to-issues` invocations are still allowed but signal process drift if used to bypass orientation.
- `@kickoff` reads the handoff doc, parent plan, AND cited ADRs at activation. For verticals with deep ADR dependency chains, this can be context-heavy. Mitigation: the cheat-sheet (Sprint 1.5.3) lets `@kickoff` skim the system surface in O(1) instead of O(N) ADRs.
- `@kickoff` and `@begin` form a paired surface; updates to one often imply review of the other for consistency. `@docs-refresh` regenerates `docs/skills/INDEX.md` (Sprint 1.5.4) automatically.

**Empirical-test footnote:** the next vertical pickup after Sprint 1.5 closes is the live-fire test. Sprint 2 vertical B/C/D are the obvious targets. If the user invokes `@kickoff` and reaches *"first action"* state with one line of input + one focused dialogue exchange, the skill proves itself. If a fresh Cascade reaches for `~/.windsurf/plans/<vertical>-kickoff-*.md` instead, that's a regression — capture to queue for the next `@sprint-review`.

**Bootstrap note:** `@kickoff` itself is authored without routing through `@kickoff` (chicken-and-egg). Sprint 1.5 authored it via this ADR + plan. Per ADR-017's footnote: meta-skills get their own ADR, then become routable for everything else.

## Source

- `~/.windsurf/plans/sprint-1-5-onboard-ramp-f8add2.md` §2 D1 + D2, §3 1.5.2 (this skill's spec)
- `cascade-system/queue/pending-review.md` "Sprint 2 prep — vertical-spawn bootstrap gap" entry (motivating friction)
- ADR-014 (canonical L1 skill path: `~/.codeium/windsurf/skills/kickoff/SKILL.md`)
- ADR-015 (skill-vs-workflow choice rationale)
- ADR-017 (`@propose-extension` intake channel — `@kickoff` was authored via the meta-skill route in batched form during plan mode)
- ADR-006 (skill frontmatter schema; line 45 + 77 corrected for path drift in Sprint 1.5.6)
- ADR-019 (`@begin` paired skill) — separates the new-idea path
- ADR-011 (supersession over deletion — applied to legacy kickoff plans)
- `cascade-system/docs/handoffs/cascade-c-obsidian.md` — exemplar handoff doc consumed by `@kickoff`
- `~/.windsurf/plans/cascade-c-obsidian-kickoff-4fa159.md` — legacy kickoff plan; superseded but retained
- `~/.codeium/windsurf/skills/{to-issues,grill-me}/SKILL.md` — sub-skills dispatched
- `~/.codeium/windsurf/global_workflows/{start-project,run-phase}.md` — sub-workflows dispatched
