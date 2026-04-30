# ADR-016: Workflow canonical path correction

**Status**: Accepted
**Date**: 2026-04-30
**Supersedes**: ADR-014 — workflow-path row only. All other ADR-014 decisions (skill path, rules path, deprecated-path prohibition, hybrid rule model) remain in force.
**Source**: Cascade discovery verification (Sprint 2 prep) — fresh Cascade session confirmed workflows were invisible in the `/`-menu despite files existing on disk. Windsurf docs confirmed the wrong path was recorded in ADR-014.

## Context

ADR-014 recorded the canonical global workflow path as:

> `~/.codeium/windsurf/workflows/<name>.md`

Five workflows were migrated there on 2026-04-30:
- `add-project-type.md`
- `commit.md`
- `recalibrate.md`
- `run-phase.md`
- `start-project.md`

In the Sprint 2 prep verification session, a fresh Cascade confirmed none of these appeared in the `/`-command menu (AC2 of the migration acceptance criteria). Windsurf docs review revealed the error:

> **Windsurf docs chunk 363 (Workflow Storage Locations table):**
>
> | Scope | Location |
> |---|---|
> | Workspace | `.windsurf/workflows/*.md` |
> | **Global** | **`~/.codeium/windsurf/global_workflows/*.md`** |
>
> Source: `https://docs.windsurf.com/llms-full.txt` position 363

The `global_` prefix is required. `~/.codeium/windsurf/workflows/` is **not scanned** by Windsurf for global workflow discovery. The Windsurf UI's `+ Global` button in the Workflows panel writes to `global_workflows/`, confirming the canonical location.

ADR-014 also confirmed: "Workflows (workspace-discovered, with a UI button for `+Global`)" — the `+Global` detail was recorded, but the path was not re-checked against the actual filesystem location that button writes to.

## Decision

The canonical global workflow path is:

```
~/.codeium/windsurf/global_workflows/<name>.md
```

`~/.codeium/windsurf/workflows/` is **deprecated** for global workflows. It is not scanned by Windsurf. Files there are invisible to the `/`-menu.

The workspace workflow path (`.windsurf/workflows/*.md`) is unchanged and correctly documented in ADR-014.

| Scope | Canonical path | Discovery |
|---|---|---|
| Global workflows | `~/.codeium/windsurf/global_workflows/<name>.md` | All workspaces on the machine |
| Workspace workflows | `.windsurf/workflows/*.md` (and parent dirs up to git root) | Current workspace only |
| System workflows (Enterprise) | OS-specific (e.g., `/etc/windsurf/workflows/`) | All workspaces |

## Migration record (2026-04-30, same day as ADR-016)

- `~/.codeium/windsurf/global_workflows/` directory created
- 5 workflow files moved from `~/.codeium/windsurf/workflows/` to `~/.codeium/windsurf/global_workflows/`:
  - `add-project-type.md`
  - `commit.md`
  - `recalibrate.md`
  - `run-phase.md`
  - `start-project.md`
- `~/.codeium/windsurf/workflows/` removed (empty after migration)
- `cascade-system/docs/manual.md` "Workflows (global)" row updated to reflect correct path
- `~/.codeium/windsurf/skills/verify-l1/SKILL.md` updated: canonical path table + deprecated-path check for `~/.codeium/windsurf/workflows/` (now empty/absent = correct)
- All skill/workflow bodies referencing the old path updated

## Alternatives considered

- **Symlink `~/.codeium/windsurf/global_workflows/` → `~/.codeium/windsurf/workflows/`** — rejected for the same reasons as ADR-014's symlink rejection: fragile, opaque, maintenance vector.
- **Leave both paths and accept the wrong one** — rejected. One path is scanned; one isn't. Leaving files at the unscanned path means perpetually invisible workflows.

## Consequences

- Fresh Cascade sessions now see all 5 global workflows in the `/`-command menu.
- `/start-project` step 6b and related path references in skill bodies and docs are updated to `global_workflows/`.
- `/verify-l1` is updated to check `global_workflows/` and flag any files remaining at the deprecated `workflows/` path.
- The workspace workflow path (`.windsurf/workflows/*.md`) is unchanged — this is the L2 path for project-scoped workflows, and it is correct.
- ADR-014 retains its original text (per ADR-011 retention policy). Its workflow-path row is superseded by this ADR.

## Verification criterion

In a fresh Cascade conversation after Windsurf restart: typing `/` in the Cascade input should list: `/add-project-type`, `/commit`, `/recalibrate`, `/run-phase`, `/start-project` alongside any workspace-level workflows.

## Source

- Windsurf docs `https://docs.windsurf.com/llms-full.txt` chunk 363 (Workflow Storage Locations table)
- Windsurf docs `https://docs.windsurf.com/windsurf/cascade/workflows`
- Sprint 2 prep verification session (this conversation) — AC2 failure diagnosis
- ADR-014 — the migration this ADR partially supersedes
