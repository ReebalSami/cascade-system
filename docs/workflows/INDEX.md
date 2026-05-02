# Workflows index — pointer to canonical L1 surface

This file is a **pointer index**. It lists every L1 workflow with a one-line description and a link to its canonical workflow location at `~/.codeium/windsurf/global_workflows/<name>.md` (per ADR-014 + ADR-016).

Workflows are **not duplicated here** — only catalogued. The single source of truth for each workflow body is the file at the canonical L1 path. To read a workflow's full procedure, hard gates, idempotency rules, and provenance, open the linked file.

Per ADR-015, workflows are deterministic procedures invoked via `/<name>` slash commands. They do **not** auto-activate by description match (only skills do that). For the skill-vs-workflow distinction, see ADR-015.

For a higher-level view of how workflows fit into the system, see `docs/cheat-sheet.md` (single-page system inventory) and `docs/architecture/system-overview.mmd` (visual layer diagram).

## Workflows

| Workflow | Concise description | Canonical path |
|---|---|---|
| `/start-project` | Bootstrap new project from L3 template (15 steps): scaffold + repo + Project + milestones + first-phase handoff | `~/.codeium/windsurf/global_workflows/start-project.md` |
| `/run-phase` | Dispatcher — reads `<project>/.windsurf/phases.yaml` and invokes the named phase's skill (no arg = list phases with status) | `~/.codeium/windsurf/global_workflows/run-phase.md` |
| `/recalibrate` | Detect/resolve drift between PRD §11 slices, GitHub state, and recent commits; per-finding triage | `~/.codeium/windsurf/global_workflows/recalibrate.md` |
| `/add-project-type` | Bootstrap new L3 template at `~/.windsurf/templates/<type>/` (12 steps + ADR) | `~/.codeium/windsurf/global_workflows/add-project-type.md` |
| `/branch-start` | (helper for `@release-manager`) — start branch or worktree from `main` with conventional-commit prefix | `~/.codeium/windsurf/global_workflows/branch-start.md` |
| `/branch-push-and-pr` | (helper) — push branch + open PR via `gh pr create --fill`; idempotent | `~/.codeium/windsurf/global_workflows/branch-push-and-pr.md` |
| `/ci-watch` | (helper) — watch PR's CI status with adaptive cadence; clean exit when no CI configured | `~/.codeium/windsurf/global_workflows/ci-watch.md` |
| `/branch-merge-and-cleanup` | (helper) — squash-merge + delete branch (remote + local) + sync `main` | `~/.codeium/windsurf/global_workflows/branch-merge-and-cleanup.md` |
| `/commit` | Safe multi-paragraph git commit (tempfile + `git commit -F`); bypasses macOS-Windsurf newline crash per ADR-013 | `~/.codeium/windsurf/global_workflows/commit.md` |

## Notes

- This index is **maintained by `@docs-refresh`**. When a new L1 workflow is authored at `~/.codeium/windsurf/global_workflows/<name>.md`, run `@docs-refresh` to regenerate this table.
- The 4 helper workflows (`/branch-*`, `/ci-watch`) compose into `@release-manager` per ADR-018, but each is also independently invocable. The skill orchestrates judgment-heavy decisions across them; the workflows execute the deterministic steps.
- The originals lived at `~/.windsurf/workflows/` until 2026-04-30 when ADR-014 relocated them, then again at `~/.codeium/windsurf/workflows/` until ADR-016 corrected the canonical path to `global_workflows/`. The two former paths are **deprecated** because Windsurf does not scan them.
- L2 (per-project) and L3 (template) workflows are **not listed here** — they're scoped to their workspace/template. Each project's `<project>/.windsurf/workflows/` is its own surface.
- Workflows do not have an `activation` field (only skills do per ADR-006). All workflows fire only when their slash command is typed.
- For a one-page summary of all artifacts plus how to extend the system, see `docs/cheat-sheet.md`.

## Source

- ADR-014 (L1 canonical storage paths) — initial migration to `~/.codeium/windsurf/`
- ADR-016 (workflows path correction) — `workflows/` → `global_workflows/` (Windsurf scans the latter)
- ADR-015 (`@` skills vs `/` workflows) — invocation syntax + skill-vs-workflow choice rationale
- ADR-013 (commit forcing function) — origin of `/commit`
- ADR-018 (release-discipline cluster) — origin of `/branch-*` helpers + `/ci-watch`
- Sprint 1.5.4 (this index) — companion to `docs/skills/INDEX.md` and `docs/rules/INDEX.md`
