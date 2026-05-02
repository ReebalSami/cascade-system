# ADR-006: SKILL.md frontmatter schema

**Status**: Accepted
**Date**: Sprint 1, M1.2 (first authored skill)

## Context

Plan §11 lists as an open question: *"Exact `sources_consulted` frontmatter schema — decided when writing the first skill."*

M1.2 (`/grill-me`) is that first skill. The schema must:

1. Be compatible with Windsurf Cascade's skill activation (description-based auto-invoke).
2. Carry provenance per `adapt-from-all` rule (`sources_consulted` + `adapted_for`).
3. Stay minimal — frontmatter that bloats discourages discipline.
4. Be uniform across all L1 skills so `/update-horizontal` can mechanically diff against upstream.

Upstream references for shape:

- **Anthropic / superpowers / mattpocock** skill format: `name` + `description` in frontmatter, body in Markdown.
- **Plan §3.4 illustrative example**: adds `sources_consulted`, `adapted_for`, `activation: auto`.
- **Our existing rule frontmatter** (in `~/.windsurf/rules/`): uses `trigger: always_on | model_decision` and `description`.

## Decision

L1 SKILL.md files use this frontmatter schema:

```yaml
---
name: <skill-slug>                         # required, kebab-case, matches directory name
description: <activation hint>             # required, used for auto-invoke matching
activation: auto | manual                  # required; "auto" = description-driven; "manual" = invoked explicitly via /<name>
sources_consulted:                         # required (may be empty list); per adapt-from-all rule
  - <provider>/<path> (<license>, <vendored ref path>)
  - <project> (own, <descriptor>)
  - <library> (browsed, no direct adoption)
adapted_for:                               # required (may be empty list); per adapt-from-all rule
  - <bullet describing the adaptation>
---
```

### Field reference

| Field | Type | Required | Notes |
|---|---|---|---|
| `name` | string | yes | kebab-case; matches `~/.codeium/windsurf/skills/<name>/SKILL.md` directory per ADR-014 |
| `description` | string | yes | one-sentence activation hint; surfaces in Cascade's skill picker; informs auto-invoke matching |
| `activation` | enum (`auto` \| `manual`) | yes | `auto` = activated when description matches conversational context; `manual` = user must explicitly invoke `/<name>` |
| `sources_consulted` | list of strings | yes | may be empty (`[]`) for fully novel skills; per `adapt-from-all` rule, every authored skill must show its work |
| `adapted_for` | list of strings | yes | bullet points naming what was changed/adapted from sources |

### Optional fields (added as needed; no version bump for additions)

- `phase` — if the skill maps to a phase taxonomy entry (e.g., `phase: brainstorm`), aids `/run-phase` resolution
- `produces_artifacts` — list of expected output paths (mirrors `phases.yaml` `artifacts` field for skills invoked outside `/run-phase`)
- `requires_skills` — skills this one calls/depends on (forward-references allowed in docs)

### Forbidden in frontmatter

- Time-based fields (`estimated_duration`, `time_to_run`) — violates `no-time-estimates`
- Version (`version: 1.0`) — frontmatter is the spec; version control is git's job
- License field — license is the project's, not the skill's

## Alternatives considered

- **Match Anthropic format exactly (`name` + `description` only)** — Rejected. Loses provenance; violates `adapt-from-all` rule.
- **Add `sources_consulted` + `adapted_for` as Markdown body sections, not frontmatter** — Rejected. Frontmatter is mechanically parseable for `/update-horizontal`'s upstream-diff loop; body sections are not.
- **Use `trigger:` like rules do** — Rejected. Skills and rules are different abstractions; `activation` is more accurate for skills (rules attach; skills activate). Keeping the field names distinct prevents confusion.
- **Drop `activation` and infer from filename pattern (e.g., `auto-*` prefix)** — Rejected. Filename conventions are fragile and not surfaced to readers.

## Consequences

- M1.2 (`/grill-me`) is the first skill authored under this schema; it's the reference example.
- All future L1 skills (M1.6, M1.8, M1.9, M1.10, M1.11) follow this schema.
- L3-template skills (Sprint 2 onward) inherit this schema; downstream projects use it too.
- `/write-skill` (M1.10) generates new skills using this schema as its template.
- `/update-horizontal` (M1.10) uses `sources_consulted` to mechanically check upstream changes.
- L1 workflows (`~/.codeium/windsurf/global_workflows/<name>.md` per ADR-014/016) use a similar but not identical schema — separate ADR if a workflow-specific decision arises (none anticipated; workflows are simpler).

## Source

- Plan `~/.windsurf/plans/cascade-project-system-cac5f9.md` §3.4 (adapt-from-all pattern), §11 (open question on schema)
- `~/.windsurf/rules/adapt-from-all.md`
- Reference skills: `refs/superpowers/skills/brainstorming/SKILL.md`, `refs/mattpocock-skills/skills/productivity/grill-me/SKILL.md`
- M1.2 issue: `ReebalSami/cascade-system#3`
