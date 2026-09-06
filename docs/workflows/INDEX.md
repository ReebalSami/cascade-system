# Workflows index — compatibility stubs (ADR-037)

This file is a **pointer index** for `~/.codeium/windsurf/global_workflows/<name>.md`. Since ADR-037 (2026-09-06) that directory holds **3-line redirect stubs only**: Devin CLI does not import Windsurf workflows (Devin docs, `reference/configuration/read-config-from.mdx`), so every procedure that used to live here is now a skill at `~/.codeium/windsurf/skills/<name>/SKILL.md` with `triggers: [user]` + `activation: manual`. The stubs exist so Windsurf's `/<name>` slash menu still resolves and nothing is deleted (ADR-011).

Do **not** add new content under `global_workflows/`. A new deterministic procedure is a manual skill — route it through `@propose-extension` → `@write-skill`.

For the skills themselves (descriptions, canonical paths, activation), see `docs/skills/INDEX.md`. For a higher-level view, see `docs/cheat-sheet.md` and `docs/architecture/system-overview.mmd`.

## Stubs

| Stub (`/<name>` in Windsurf) | Redirects to | Skill path |
|---|---|---|
| `/start-project` | `@start-project` | `~/.codeium/windsurf/skills/start-project/SKILL.md` |
| `/run-phase` | `@run-phase` | `~/.codeium/windsurf/skills/run-phase/SKILL.md` |
| `/recalibrate` | `@recalibrate` | `~/.codeium/windsurf/skills/recalibrate/SKILL.md` |
| `/add-project-type` | `@add-project-type` | `~/.codeium/windsurf/skills/add-project-type/SKILL.md` |
| `/branch-start` | `@branch-start` | `~/.codeium/windsurf/skills/branch-start/SKILL.md` |
| `/branch-push-and-pr` | `@branch-push-and-pr` | `~/.codeium/windsurf/skills/branch-push-and-pr/SKILL.md` |
| `/ci-watch` | `@ci-watch` | `~/.codeium/windsurf/skills/ci-watch/SKILL.md` |
| `/branch-merge-and-cleanup` | `@branch-merge-and-cleanup` | `~/.codeium/windsurf/skills/branch-merge-and-cleanup/SKILL.md` |
| `/commit` | `@commit` | `~/.codeium/windsurf/skills/commit/SKILL.md` |
| `/issue-create` | `@issue-create` | `~/.codeium/windsurf/skills/issue-create/SKILL.md` |

Stub shape (every file):

```markdown
---
description: "Superseded by the @<name> skill (ADR-037); invoke it."
---
Superseded by the `@<name>` skill (ADR-037); invoke it.
```

## Notes

- In Devin CLI, `/<name>` invokes the skill directly; the stub directory is not read at all.
- In Windsurf, typing `/<name>` loads the stub, which tells Cascade to invoke `@<name>`; the skill's `description` starts with "Manual: …" so it never auto-activates by description match.
- `@verify-l1` step 4 checks that every stub has a `description`, redirects to an existing skill, and has not grown a body (content belongs in the skill).
- History: the originals lived at `~/.windsurf/workflows/` until 2026-04-30 (ADR-014), then `~/.codeium/windsurf/workflows/` until ADR-016 corrected the path to `global_workflows/`, then were re-homed as skills by ADR-037. The two former paths are **deprecated**; `global_workflows/` is stubs-only.
- L2 (per-project) and L3 (template) `workflows/` directories are likewise deprecated: `/start-project` deploys any legacy L3 `workflows/` into `<project>/.agents/skills/` for compatibility and `/add-project-type` no longer proposes them.

## Source

- ADR-037 (Devin runtime adoption cluster) — workflows → skills; stubs kept per ADR-011
- ADR-014 (L1 canonical storage paths) — initial migration to `~/.codeium/windsurf/`
- ADR-016 (workflows path correction) — `workflows/` → `global_workflows/` (Windsurf scans the latter)
- ADR-015 (`@` skills vs `/` workflows) — original invocation syntax; invocation table superseded by ADR-037
- ADR-013 (commit forcing function) — origin of `/commit`
- ADR-018 (release-discipline cluster) — origin of `/branch-*` helpers + `/ci-watch`
- ADR-036 (issue-project-assignment rule) — origin of `/issue-create`
- Sprint 1.5.4 (this index) — companion to `docs/skills/INDEX.md` and `docs/rules/INDEX.md`
