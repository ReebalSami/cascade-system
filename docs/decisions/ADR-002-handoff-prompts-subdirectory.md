# ADR-002: `docs/handoffs/` subdirectory for cross-Cascade handoff prompts

**Status**: Accepted
**Date**: Sprint 0 → Sprint 1 transition

## Context

Plan §3.10 (Documentation system) prescribes the following `docs/` structure:

```
docs/
├── structure.md
├── makefile-manual.md
├── technical.md
├── architecture/
├── decisions/
├── retros/
└── prompts/stages/
```

At the Sprint 0 → Sprint 1 transition, we needed to write a handoff prompt — a self-contained instruction set for a fresh Cascade picking up the next sprint. The prompt is a meta-artifact: it's not a project-phase artifact (so not `prompts/stages/`), not a retro (post-hoc, not pre-sprint), not architecture, not a decision record itself.

I (Cascade A) initially placed it at `docs/handoff-cascade-a-sprint-1.md` — `docs/` root. User correctly flagged this as violating the structure prescribed in §3.10 and the `document-as-you-go` rule (which I had just authored in M0.5).

## Decision

Add `docs/handoffs/` as a new top-level subdirectory under `docs/`.

**Naming convention**: `<from-cascade-id>-<to-sprint-or-vertical>.md`

Examples:
- `cascade-a-sprint-1.md` — Cascade A handing off to Sprint 1
- `cascade-a-sprint-2.md`
- `cascade-b-template-python-ml-uv.md` — Cascade A spawning Cascade B for a vertical
- `cascade-c-obsidian.md`
- `cascade-d-thesis.md`

**Contents**: A self-contained prompt that bootstraps a fresh Cascade entirely from disk + GitHub state — no chat history needed. Includes:

- The role the new Cascade plays
- Ordered list of paths to read for context
- The work to do (which sprint / which milestones)
- Operating constraints (which principle rules apply)
- Termination criteria (when this handoff ends)

## Alternatives considered

- **Place under `docs/prompts/`** — Rejected. `prompts/stages/` is for project-phase artifacts (the user-facing project's lifecycle, e.g., `01-literature.md`, `03-prd.md`). Cross-Cascade handoff prompts are meta — about the system itself, not about a downstream project's phases.
- **Keep flat in `docs/` root** — Rejected. Bloats the root once we have multiple handoffs (every sprint boundary, every vertical spawn). Also tested poorly: I made the mistake immediately on the first one.
- **Put in `retros/`** — Rejected. Retros are post-hoc; handoffs are pre-sprint. Different temporality, different audience (retros are for past-me reflecting; handoffs are for future-me/another-Cascade picking up).
- **Put in `decisions/`** — Rejected. Handoffs aren't decisions; they're operational instructions.
- **Inline in README** — Rejected. README would balloon; handoffs are sprint-specific and should be discoverable per-sprint.

## Consequences

- Plan §3.10 needs an addendum to mention `docs/handoffs/` (will edit the plan inline as part of this change — the plan is living, this ADR is the canonical record of the decision).
- `/start-project` (M1.4) should generate a handoff prompt in `<new-project>/docs/handoffs/` when bootstrapping a project that spans multiple sprints.
- `/run-phase` (M1.5) may emit a handoff prompt at phase boundaries if the user wants to swap Cascades between phases.
- `/docs-refresh` (M1.11) validates placement: anything ending in `*.md` at the wrong directory level gets flagged.
- A `docs/handoffs/INDEX.md` file lists known handoff prompts with a one-line summary each.

## Source

- User feedback at Sprint 0 → Sprint 1 transition: "i thought we talked about having a good structure of docs"
- Cascade A's own `document-as-you-go` rule (M0.5) which mandates ADRs at the moment of decision
- Plan: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §3.10
