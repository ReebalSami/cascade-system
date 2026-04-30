# ADR-014: L1 canonical storage paths

**Status**: Accepted
**Date**: 2026-04-30
**Supersedes**: none (extends ADR-006 path conventions; ADR-012/013 references to `~/.windsurf/workflows/commit.md` remain as historical record but their canonical path is now `~/.codeium/windsurf/workflows/commit.md`)
**Source**: User-driven discovery during Sprint 1 hardening — `@skills:` returned empty results in fresh Cascade conversations despite skills being authored under `~/.windsurf/skills/`. Windsurf documentation review (`https://docs.windsurf.com/llms-full.txt` chunks 30, 128, 340, 347, 350, 353) confirmed Windsurf does not scan `~/.windsurf/`.

## Context

The Sprint 1 system was authored with global L1 components placed under `~/.windsurf/`:

- 14 rules at `~/.windsurf/rules/*.md`
- 8 skills at `~/.windsurf/skills/<name>/SKILL.md`
- 5 workflows at `~/.windsurf/workflows/<name>.md`

Empirically, none of these were visible to Cascade: `@skills:` mentions returned empty, slash commands defined in `~/.windsurf/workflows/` did not appear in the slash-command list, and rules from `~/.windsurf/rules/` did not load into the system prompt.

The Windsurf documentation specifies different paths:

- Global skills: `~/.codeium/windsurf/skills/<name>/SKILL.md`
- Workflows (workspace-discovered, with a UI button for `+Global`): `~/.codeium/windsurf/workflows/<name>.md`
- Global rules: a **single file** at `~/.codeium/windsurf/memories/global_rules.md` with a **6,000-character cap** and no frontmatter

Windsurf does not support multi-file global rules. The user's 14 rules totaled ~39 KB — they cannot all fit in `global_rules.md`.

The diagnosis: `~/.windsurf/` is a directory the user (and the authoring Cascade) invented. Windsurf doesn't scan it. The "missing skills" was not an ignore-file problem, not an MCP config problem, not a frontmatter problem — it was wrong-path.

Workspace-level paths (`<workspace>/.windsurf/...`) DO work — the existing `/review` workflow at `~/Projects/.windsurf/workflows/review.md` was discovered correctly. But workspace rules walk up only **to the git root**, so `~/Projects/.windsurf/rules/` doesn't apply when working inside a project repo. Truly cross-project loading requires either `global_rules.md` or per-project copies.

## Decision

L1 components live at the following canonical paths:

| Component | Canonical path | Notes |
|---|---|---|
| Skills | `~/.codeium/windsurf/skills/<name>/SKILL.md` | Multi-file. Windsurf-discovered. |
| Workflows | `~/.codeium/windsurf/workflows/<name>.md` | Single-file each. Windsurf-discovered. |
| **Rules — concise/active** | `~/.codeium/windsurf/memories/global_rules.md` | Single file, ≤6,000 chars, always-on, no frontmatter. The LAW. |
| **Rules — long-form/reference** | `~/Projects/cascade-system/docs/rules/<name>.md` | Multi-file with full frontmatter, rationale, provenance. The COMMENTARY. Not auto-loaded. |
| **Rules — per-project** | `<project>/.windsurf/rules/<name>.md` | Workspace-scoped. Populated by `/start-project` step 6a from the long-form archive. |
| Contracts | `~/.windsurf/contracts/<name>.md` | Agent-internal. Not Windsurf-loaded. Stays put. |
| Templates | `~/.windsurf/templates/{<type>,_shared}/` | Agent-internal. Stays put. |
| Plans | `~/.windsurf/plans/<slug>-<suffix>.md` | Agent-internal. Stays put. |

The `~/.windsurf/{skills,workflows,rules}/` paths are **deprecated** and must remain empty. A future `/verify-l1` skill enforces this.

For rule mutations, `/update-horizontal` enforces dual-update: edit BOTH the long-form archive AND the corresponding section of `global_rules.md`. The two are siblings, not source/derived. Verify `wc -c` on `global_rules.md` stays ≤6000 after every edit.

## Alternatives considered

- **Symlink `~/.codeium/windsurf/skills/` → `~/.windsurf/skills/`** — fragile (symlinks break on filesystem operations, cloud sync, fresh-machine restore), opaque (the canonical location becomes path-dependent), and adds a maintenance vector. Rejected.

- **Move everything to `~/.codeium/windsurf/`** including rules concatenated into `global_rules.md` and dropping the long-form archive — rejected because the 6 KB cap forces extreme concision (1-line rules without rationale, examples, or provenance), losing the institutional knowledge captured in `sources_consulted` and `adapted_for` frontmatter.

- **Workspace-only rules at `~/Projects/.windsurf/rules/`** — rejected because workspace rule discovery walks up only to the git root. Inside a project repo (most user work happens there), rules at `~/Projects/.windsurf/` are invisible. Loses the always-on guarantee.

- **Cherry-pick 5 critical rules; drop the rest** — rejected because all 14 rules are foundational and were authored deliberately. Dropping them means losing institutional discipline.

- **Hybrid (chosen)**: concise global manifest + long-form archive + per-project copies via `/start-project`. Cost: rule edits require dual-update; per-project copies go stale on global edit until manually re-synced. Benefit: every Cascade conversation gets the law (regardless of project), every project has full long-form available, the archive preserves rationale and provenance.

## Consequences

**Enables:**

- `@skills:` returns the authored skill set in any fresh Cascade conversation.
- Slash commands authored under `~/.codeium/windsurf/workflows/` appear in the slash-command list.
- Global rules load into every conversation regardless of which project the user opens.
- Per-project rule overrides remain possible at workspace scope (L3 templates can ship rules that override globals via last-wins copy in `/start-project` step 6b).

**Constrains:**

- `global_rules.md` capped at 6,000 chars. New always-on rules require trimming existing content or downgrading low-priority rules to project-only.
- Rule edits are now two-place: long-form archive + concise manifest. `/update-horizontal` enforces this; manual edits outside the skill risk drift.
- Per-project rule copies become stale when a global rule is edited. Mitigation: a future `/sync-rules` workflow, or manual `cp` from archive. Documented in `update-horizontal` propagation table.
- The L1 layer no longer has a single root directory — it spans `~/.codeium/windsurf/`, `~/.windsurf/contracts/`, `~/.windsurf/templates/`, and `~/Projects/cascade-system/docs/rules/`. Tooling (`/verify-l1`, `/sprint-review`) must enumerate all of them.

**Migration record (2026-04-30):**

- 8 skills moved: `~/.windsurf/skills/*` → `~/.codeium/windsurf/skills/*`
- 5 workflows moved: `~/.windsurf/workflows/*` → `~/.codeium/windsurf/workflows/*`
- 14 rules archived: `~/.windsurf/rules/*` → `~/Projects/cascade-system/docs/rules/*` (full bodies, frontmatter intact)
- `global_rules.md` authored at 5,822 chars (~178 chars headroom)
- `/start-project` step 6 expanded to 6a (global rules copy) + 6b (L3 overrides)
- `/update-horizontal` updated for dual-update on rule edits
- `/sprint-review` step 8 (clean working tree gate) updated for the new repo layout (`~/.codeium/windsurf/` and `~/.windsurf/` are NOT git repos; warn instead of git-status-check)
- `/verify-l1` skill authored to validate ongoing layout consistency
- `~/.windsurf/{skills,workflows,rules}/` snapshot to `~/.windsurf.bak-2026-04-30/` before move; original directories left empty
- Path references updated in: `cascade-system/README.md`, `docs/manual.md` (new), `docs/decisions/INDEX.md`, `docs/handoffs/cascade-{b,c}-*.md`, `queue/pending-review.md`, all skills/workflows under `~/.codeium/windsurf/`, archive copies of all 14 rules
- Path references intentionally NOT updated: `retros/sprint-1.md` (closed retro, immutable per ADR-011), `docs/reviews/sprint-1-review.md` (historical snapshot), `docs/handoffs/cascade-a-sprint-1.md` (closed-sprint handoff), ADRs 003/006/012/013 (immutable historical decisions)

**Rollback:**

If Windsurf changes its scanning behavior (e.g., adds support for `~/.windsurf/`, or moves the canonical path again), the rollback is:

1. Move skills/workflows back to wherever Windsurf scans.
2. Adjust `/start-project` step 6a path.
3. Adjust `/update-horizontal` dual-update target.
4. Author a new ADR superseding this one.

The backup at `~/.windsurf.bak-2026-04-30/` (preserved per Phase 0 of the migration plan) is the recovery snapshot for at least one full sprint.

## Verification

Acceptance criteria from `~/.windsurf/plans/l1-visibility-migration-71dca8.md`:

1. `@skill:` mention shows authored skills (8 of them) ✓ pending fresh Cascade verification
2. `/commit` is in the slash-command list of a fresh Cascade ✓ pending verification
3. `wc -c global_rules.md` returns >0 and ≤6000 → currently 5851 bytes / 5822 chars ✓
4. Fresh Cascade echoes a global rule verbatim when asked ✓ pending verification
5. `/start-project` dry-run produces `.windsurf/rules/` with all 14 long-form rules ✓ pending dry-run
6. `/verify-l1` runs clean ✓ pending invocation
7. `grep -rl '~/\.windsurf/{skills,workflows,rules}/'` returns zero matches outside intentional historical references ✓ verified

Items 1-2, 4-6 require a fresh Cascade conversation; verifying within the migration session is unreliable due to context bleed.
