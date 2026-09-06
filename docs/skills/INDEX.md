# Skills index — pointer to canonical L1 surface

This file is a **pointer index**. It lists every L1 skill with a one-line description and a link to its canonical SKILL.md location at `~/.codeium/windsurf/skills/<name>/SKILL.md` (per ADR-014; read by both Devin CLI and Windsurf per ADR-037).

Skills are **not duplicated here** — only catalogued. The single source of truth for each skill body is the file at the canonical L1 path. To read a skill's full procedure, frontmatter, hard gates, anti-patterns, and provenance, open the linked file.

Invocation (per ADR-037): every skill is `/<name>` in Devin CLI and `@<name>` in Windsurf. Skills marked **manual** carry `triggers: [user]` + `activation: manual` — no agent auto-invokes them; they are the former workflows (ADR-015's `/`-only layer), re-homed. For the redirect stubs that keep Windsurf's `/<name>` menu working, see `docs/workflows/INDEX.md`.

For a higher-level view of how skills fit into the system, see `docs/cheat-sheet.md` (single-page system inventory) and `docs/architecture/system-overview.mmd` (visual layer diagram).

## Skills

| Skill | Activation | Concise description | Canonical path |
|---|---|---|---|
| `grill-me` | auto | Socratic interview that walks the design tree until shared understanding | `~/.codeium/windsurf/skills/grill-me/SKILL.md` |
| `to-prd` | auto | Convert approved brainstorm artifact → PRD using the shared template | `~/.codeium/windsurf/skills/to-prd/SKILL.md` |
| `to-issues` | auto | Decompose PRD §11 vertical slices → GitHub issues + milestones + Project cards | `~/.codeium/windsurf/skills/to-issues/SKILL.md` |
| `sync-github` | auto | Reconcile GitHub Project board with repo signals; flag naked commits; idempotent | `~/.codeium/windsurf/skills/sync-github/SKILL.md` |
| `sprint-review` | auto | Heartbeat retrospective: drain queue, write retro, propose L1 promotions, hand off to `update-horizontal` | `~/.codeium/windsurf/skills/sprint-review/SKILL.md` |
| `update-horizontal` | auto | Apply L1 change (rule/skill/contract/template); writes change ADR; propagates downstream | `~/.codeium/windsurf/skills/update-horizontal/SKILL.md` |
| `verify-l1` | auto | Validate L1 storage layout against ADR-014/016/037; strict-YAML frontmatter parse + `devin skills list` cross-check; 6000-char rule cap; archive↔global drift; dead-path sweeps | `~/.codeium/windsurf/skills/verify-l1/SKILL.md` |
| `docs-refresh` | auto | Validate + regenerate `docs/` placement; regenerate INDEX files; audit diagrams | `~/.codeium/windsurf/skills/docs-refresh/SKILL.md` |
| `release-manager` | auto | Orchestrate branch lifecycle: branch → commits → push → PR → CI → squash-merge → cleanup (delegates to the four manual helper skills below) | `~/.codeium/windsurf/skills/release-manager/SKILL.md` |
| `propose-extension` | auto | Single intake channel for any system extension; 5-question interview, route table | `~/.codeium/windsurf/skills/propose-extension/SKILL.md` |
| `write-skill` | auto | Author/edit a skill body (frontmatter, structure, sources_consulted, strict-YAML gate, index updates) | `~/.codeium/windsurf/skills/write-skill/SKILL.md` |
| `begin` | auto | Front door for new projects: `grill-me` → stack decision → `/add-project-type` (if needed) → `/start-project` → `/run-phase brainstorm` (ADR-019) | `~/.codeium/windsurf/skills/begin/SKILL.md` |
| `kickoff` | auto | Front door for vertical pickups from a handoff doc: reads handoff + plan + ADRs + cheat-sheet, detects lifecycle position, files missing issues, asks one question (ADR-020) | `~/.codeium/windsurf/skills/kickoff/SKILL.md` |
| `handoff-to-coding-session` | auto | Write a context-bundle handoff for a fresh coding-optimal session (ADR-034) | `~/.codeium/windsurf/skills/handoff-to-coding-session/SKILL.md` |
| `handoff-to-thinking-session` | auto | Write a context-bundle handoff for a fresh decision-optimal session (ADR-034) | `~/.codeium/windsurf/skills/handoff-to-thinking-session/SKILL.md` |
| `vault-research` | auto | Surface ranked vault notes for a topic via structural composite ranking (recency × tag-overlap × MOC-distance); deliberately not embedding-based per ADR-023 hand-curated vault. READ bookend of `vault-distill` | `~/.codeium/windsurf/skills/vault-research/SKILL.md` |
| `vault-distill` | auto | Compile raw vault sources → `wiki/` cards per ADR-023 §8 ingest workflow. Dry-run by default (tempdir preview under `_meta/.preview/` with edit-before-commit); `--commit` atomic write with SHA-256 integrity check. §9 quality gate as flagging. Refuses `originals/` / `_meta/` / `wiki/` / outside-vault. WRITE bookend of `vault-research` | `~/.codeium/windsurf/skills/vault-distill/SKILL.md` |
| `start-project` | **manual** | Bootstrap new project from L3 template (15 steps): two-pass scaffold + `.devin/phases.yaml` + six on-demand rules into `.devin/rules/` + L3 skills into `.agents/skills/` + repo + branch protection + Project + milestones + first-phase handoff. `--dry-run` supported | `~/.codeium/windsurf/skills/start-project/SKILL.md` |
| `run-phase` | **manual** | Dispatcher — reads `<project>/.devin/phases.yaml` (legacy `.windsurf/` fallback) and hands off to the named phase's skill (no arg = list phases with status) | `~/.codeium/windsurf/skills/run-phase/SKILL.md` |
| `recalibrate` | **manual** | Detect/resolve drift between PRD §11 slices, GitHub state, recent commits, vault state; per-finding triage | `~/.codeium/windsurf/skills/recalibrate/SKILL.md` |
| `add-project-type` | **manual** | Bootstrap new L3 template at `~/.windsurf/templates/<type>/` (12 steps + ADR + dry-run) | `~/.codeium/windsurf/skills/add-project-type/SKILL.md` |
| `commit` | **manual** | Safe multi-paragraph git commit (tempfile + `git commit -F`); bypasses the macOS-Windsurf newline crash per ADR-013 | `~/.codeium/windsurf/skills/commit/SKILL.md` |
| `branch-start` | **manual** | (helper for `release-manager`) — start branch or worktree from `main` with conventional-commit prefix | `~/.codeium/windsurf/skills/branch-start/SKILL.md` |
| `branch-push-and-pr` | **manual** | (helper) — push branch + open PR after user approves the drafted description; idempotent | `~/.codeium/windsurf/skills/branch-push-and-pr/SKILL.md` |
| `ci-watch` | **manual** | (helper) — watch PR's CI status with adaptive cadence; clean exit when no CI configured | `~/.codeium/windsurf/skills/ci-watch/SKILL.md` |
| `branch-merge-and-cleanup` | **manual** | (helper) — 4-option closeout; squash-merge + delete branch (remote + local) + sync `main` | `~/.codeium/windsurf/skills/branch-merge-and-cleanup/SKILL.md` |
| `issue-create` | **manual** | Create a GitHub issue and atomically add it to the Project v2 board; forcing function for `issue-project-assignment-required` (ADR-036) | `~/.codeium/windsurf/skills/issue-create/SKILL.md` |

## Notes

- This index is **maintained by `@docs-refresh`**. When a new L1 skill is authored at `~/.codeium/windsurf/skills/<name>/SKILL.md`, run `@docs-refresh` to regenerate this table (and `@write-skill` step 9 updates it directly at authoring time). When a skill is renamed or its description changes, the same skill picks it up.
- The originals lived at `~/.windsurf/skills/` until 2026-04-30 when ADR-014 relocated them — that path is **deprecated** because neither tool scans it.
- The ten **manual** skills were `~/.codeium/windsurf/global_workflows/*.md` until ADR-037 (2026-09-06) re-homed them; Devin CLI does not import workflows. Their old files are 3-line redirect stubs.
- L2 (per-project) and L3 (template) skills are **not listed here** — they're scoped to their workspace/template. Each project's `<project>/.agents/skills/` is its own surface (legacy `.windsurf/skills/` in pre-ADR-037 projects).
- Activation column reflects each skill's `activation:` frontmatter field per ADR-006. `auto` = the agent activates by description match; **manual** = `triggers: [user]`, explicit invocation only.
- Every frontmatter block must parse as strict YAML (ADR-006 amendment); `@verify-l1` step 3 checks this and cross-checks `devin skills list`.
- For a one-page summary of all artifacts (skills + stubs + rules + contracts + templates) plus how to extend the system, see `docs/cheat-sheet.md`.

## Source

- ADR-014 (L1 canonical storage paths) — defines `~/.codeium/windsurf/skills/` as authoritative
- ADR-006 (skill frontmatter schema) — defines the `activation` field; amended by ADR-037 with `triggers`, `argument-hint`, strict-YAML clause
- ADR-015 (`@` skills vs `/` workflows) — original invocation syntax; invocation table superseded by ADR-037
- ADR-037 (Devin runtime adoption cluster) — workflows re-homed as manual skills
- Sprint 1.5.4 (this index) — companion to `docs/workflows/INDEX.md` and `docs/rules/INDEX.md`
