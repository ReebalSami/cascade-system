# Skills index — pointer to canonical L1 surface

This file is a **pointer index**. It lists every L1 skill with a one-line description and a link to its canonical SKILL.md location at `~/.codeium/windsurf/skills/<name>/SKILL.md` (per ADR-014).

Skills are **not duplicated here** — only catalogued. The single source of truth for each skill body is the file at the canonical L1 path. To read a skill's full procedure, frontmatter, hard gates, anti-patterns, and provenance, open the linked file.

For a higher-level view of how skills fit into the system, see `docs/cheat-sheet.md` (single-page system inventory) and `docs/architecture/system-overview.mmd` (visual layer diagram).

## Skills

| Skill | Activation | Concise description | Canonical path |
|---|---|---|---|
| `@grill-me` | auto | Socratic interview that walks the design tree until shared understanding | `~/.codeium/windsurf/skills/grill-me/SKILL.md` |
| `@to-prd` | auto | Convert approved brainstorm artifact → PRD using the shared template | `~/.codeium/windsurf/skills/to-prd/SKILL.md` |
| `@to-issues` | auto | Decompose PRD §11 vertical slices → GitHub issues + milestones + Project cards | `~/.codeium/windsurf/skills/to-issues/SKILL.md` |
| `@sync-github` | auto | Reconcile GitHub Project board with repo signals; flag naked commits; idempotent | `~/.codeium/windsurf/skills/sync-github/SKILL.md` |
| `@sprint-review` | auto | Heartbeat retrospective: drain queue, write retro, propose L1 promotions, hand off to `@update-horizontal` | `~/.codeium/windsurf/skills/sprint-review/SKILL.md` |
| `@update-horizontal` | auto | Apply L1 change (rule/skill/workflow/contract/template); writes change ADR; propagates downstream | `~/.codeium/windsurf/skills/update-horizontal/SKILL.md` |
| `@verify-l1` | auto | Validate L1 storage layout against ADR-014; check 6000-char rule cap; flag drift | `~/.codeium/windsurf/skills/verify-l1/SKILL.md` |
| `@docs-refresh` | auto | Validate + regenerate `docs/` placement; regenerate INDEX files; audit diagrams | `~/.codeium/windsurf/skills/docs-refresh/SKILL.md` |
| `@release-manager` | auto | Orchestrate branch lifecycle: branch → commits → push → PR → CI → squash-merge → cleanup | `~/.codeium/windsurf/skills/release-manager/SKILL.md` |
| `@propose-extension` | auto | Single intake channel for any system extension; 5-question interview, route table | `~/.codeium/windsurf/skills/propose-extension/SKILL.md` |
| `@write-skill` | auto | Author/edit a skill body (frontmatter, structure, sources_consulted) | `~/.codeium/windsurf/skills/write-skill/SKILL.md` |
| `@vault-research` | auto | Surface ranked vault notes for a topic via structural composite ranking (recency × tag-overlap × MOC-distance); deliberately not embedding-based per ADR-023 hand-curated vault. READ bookend of `@vault-distill` | `~/.codeium/windsurf/skills/vault-research/SKILL.md` |
| `@vault-distill` | auto | Compile raw vault sources → `wiki/` cards per ADR-023 §8 ingest workflow. Dry-run by default (tempdir preview under `_meta/.preview/` with edit-before-commit); `--commit` atomic write with SHA-256 integrity check. §9 quality gate as flagging. Refuses `originals/` / `_meta/` / `wiki/` / outside-vault. WRITE bookend of `@vault-research` | `~/.codeium/windsurf/skills/vault-distill/SKILL.md` |

## Notes

- This index is **maintained by `@docs-refresh`**. When a new L1 skill is authored at `~/.codeium/windsurf/skills/<name>/SKILL.md`, run `@docs-refresh` to regenerate this table. When a skill is renamed or its description changes, the same skill picks it up.
- The originals lived at `~/.windsurf/skills/` until 2026-04-30 when ADR-014 relocated them — that path is **deprecated** because Windsurf does not scan it.
- L2 (per-project) and L3 (template) skills are **not listed here** — they're scoped to their workspace/template. Each project's `<project>/.windsurf/skills/` is its own surface.
- Activation column reflects each skill's `activation:` frontmatter field per ADR-006. All current L1 skills are `auto` (Cascade activates them by description match against user phrasing).
- For a one-page summary of all artifacts (skills + workflows + rules + contracts + templates) plus how to extend the system, see `docs/cheat-sheet.md`.

## Source

- ADR-014 (L1 canonical storage paths) — defines `~/.codeium/windsurf/skills/` as authoritative
- ADR-006 (skill frontmatter schema) — defines the `activation` field
- ADR-015 (`@` skills vs `/` workflows) — invocation syntax
- Sprint 1.5.4 (this index) — companion to `docs/workflows/INDEX.md` and `docs/rules/INDEX.md`
