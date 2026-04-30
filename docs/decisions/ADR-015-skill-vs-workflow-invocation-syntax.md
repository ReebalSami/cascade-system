# ADR-015: Skill vs workflow invocation syntax

**Status**: Accepted
**Date**: 2026-04-30
**Source**: Cascade discovery verification (Sprint 2 prep) — fresh Cascade session confirmed the `/<skill-name>` convention used throughout Sprint 1 skill bodies is wrong. Windsurf docs review at `https://docs.windsurf.com/windsurf/cascade/skills` (chunk 352) and `https://docs.windsurf.com/windsurf/cascade/workflows` (chunk 361) confirmed the correct syntax.

## Context

Sprint 1 authored 9 skills and 5 workflows. Throughout the skill bodies, cross-references, and documentation, skills were referred to using slash-command syntax:

- `"via /to-prd (M1.6)"` — wrong (to-prd is a skill, not a workflow)
- `"Run /sprint-review"` — wrong (sprint-review is a skill)
- `"invoke /grill-me"` — wrong (grill-me is a skill)

This created two problems:

1. **User confusion**: users believe they must type `/grill-me` to invoke the skill, when in fact Windsurf only supports `/` for workflows. The `@mention` syntax is required for manual skill invocation.
2. **Model confusion**: when Cascade reads its own skill bodies and sees `/to-prd`, it may attempt to invoke a workflow named `to-prd`, which does not exist, rather than activating the skill.

The Sprint 1 retro noted milestone artifacts as-shipped; the syntax error was discovered during the Sprint 2 prep verification session (ADR-014's acceptance criteria required fresh-Cascade verification, which surfaced this).

## Windsurf invocation model (confirmed against docs)

| Artifact type | Invocation syntax | Activation |
|---|---|---|
| **Skill** | `@<skill-name>` (manual) | Also auto-activates when description matches conversational context (progressive disclosure — only name+description shown until invoked) |
| **Workflow** | `/<workflow-name>` (manual slash command) | **Never** auto-invoked by Cascade |
| **Rule (always_on)** | n/a — included in every system prompt | Always present in context |
| **Rule (model_decision)** | n/a — description in system prompt, full body loaded on demand | Cascade reads full body when description is relevant |
| **Rule (manual)** | `@<rule-name>` | Only when user @mentions it |

Source: Windsurf docs `https://docs.windsurf.com/llms-full.txt` chunks 352 (Skills/Invoking), 356 (Skills vs Rules vs Workflows comparison table), 361 (Workflows/manual-only note).

## Decision

1. **Skills are always `@<name>`** — for manual invocation, users type `@grill-me`, `@to-prd`, `@sprint-review`, etc. For automatic invocation, the user describes their intent in plain text and Cascade decides whether the skill's description matches.

2. **Workflows are always `/<name>`** — users type `/start-project`, `/run-phase`, `/commit`, etc. Cascade never invokes a workflow automatically.

3. All existing Sprint 1 L1 surfaces (skill bodies, workflow bodies, global_rules.md, long-form rule archive, manual.md, queue) are patched in the same commit that introduces this ADR, replacing `/<skill-name>` with `@<skill-name>` for all 9 skill names. Real workflow references (`/commit`, `/start-project`, `/run-phase`, `/recalibrate`, `/add-project-type`) are preserved.

4. Future L1 authoring uses this convention. The `@write-skill` and `/update-horizontal` distinction is now canonical (the former is a skill; the latter is a workflow named update-horizontal… wait — `update-horizontal` is a skill too, so `@update-horizontal`).

## Alternatives considered

- **Make all 9 skills into workflows** — rejected. Skills use progressive disclosure (name+description only in system prompt; body loaded on demand), which keeps the context window lean with 9+ procedures. Workflows don't have this property — their description is listed in the slash-command menu but the body only loads on invocation. For multi-file procedures with supporting assets, skills are the right abstraction.
- **Keep `/skill-name` and add actual workflows as thin dispatchers** — rejected. Double indirection with no benefit; the `@` mechanism is already the correct dispatch path for skills.
- **Leave the syntax wrong** — rejected. Model confusion is a real failure mode; Cascade reading its own skill bodies and attempting to invoke `/grill-me` as a slash command will fail silently.

## Consequences

- All 9 skill names in L1 surfaces now use `@<name>` — a one-time mechanical rewrite.
- Closed retros (`retros/sprint-1.md`), the sprint-1 review, and the sprint-1 handoff retain the old syntax for historical fidelity (per ADR-011 retention policy). These are immutable records.
- ADRs 001–014 retain old syntax where present (immutable per ADR-011 extension).
- New skill authoring via `@write-skill` uses `@<name>` in skill descriptions and cross-references.
- The `grill-me` skill body currently says "next step via `/to-prd`" — this is fixed to `@to-prd` in the same patch.

## Source

- Windsurf docs `https://docs.windsurf.com/windsurf/cascade/skills` — chunks 347, 350, 352, 353, 356
- Windsurf docs `https://docs.windsurf.com/windsurf/cascade/workflows` — chunks 361, 362, 363
- Sprint 2 prep discovery session (this conversation)
- `ADR-014` — L1 canonical paths (the migration that put files at the right paths; this ADR corrects only the invocation syntax convention, not the storage paths)
