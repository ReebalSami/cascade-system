# Cascade system manual

The day-to-day operating guide for the Cascade lifecycle system. Covers what lives where, how to make changes safely, and how to start new work. Authoritative for "where" questions; refer to ADRs for "why" questions.

## Where things live

The L1 layer (global rules, skills, workflows, contracts, templates) is split across two roots because Windsurf only scans one of them and global rules have a hard size cap. Per ADR-014:

| Component | Canonical path | Notes |
|---|---|---|
| **Skills (global)** | `~/.codeium/windsurf/skills/<name>/SKILL.md` | Multi-file. Discovered automatically by Windsurf. Frontmatter: `name`, `description`, `activation`, `sources_consulted`, `adapted_for`. |
| **Workflows (global)** | `~/.codeium/windsurf/global_workflows/<name>.md` | Single-file each. Slash-command name = filename. Frontmatter: `description`. (Per ADR-016; `~/.codeium/windsurf/workflows/` is deprecated.) |
| **Rules (global, concise/active)** | `~/.codeium/windsurf/memories/global_rules.md` | **Single file, ≤6000 chars.** Always-on, no frontmatter. The LAW that loads in every conversation. |
| **Rules (global, long-form/reference)** | `~/Projects/cascade-system/docs/rules/<name>.md` | Multi-file with full frontmatter, rationale, provenance. The COMMENTARY. Not auto-loaded; available via reading. |
| **Rules (per-project)** | `<project>/.windsurf/rules/<name>.md` | Workspace-scoped. Loaded only when Cascade is opened inside that project repo. Populated by `/start-project` step 6a from the long-form archive. |
| **Skills (per-project)** | `<project>/.windsurf/skills/<name>/SKILL.md` | Workspace-scoped. Populated by `/start-project` step 6b from the L3 template if any. |
| **Workflows (per-project)** | `<project>/.windsurf/workflows/<name>.md` | Workspace-scoped. Populated by `/start-project` step 6b. |
| **Contracts (L1)** | `~/.windsurf/contracts/<name>.md` | Agent-internal — not Windsurf-loaded; consumed by skills/workflows that read them explicitly. |
| **Templates (L3)** | `~/.windsurf/templates/<type>/` and `~/.windsurf/templates/_shared/` | Agent-internal; consumed by `/start-project` and `/add-project-type`. |
| **Plans (current/historical)** | `~/.windsurf/plans/<slug>-<suffix>.md` | Cascade-authored plans before implementation. Not Windsurf-loaded. |
| **ADRs** | `~/Projects/cascade-system/docs/decisions/ADR-NNN-<slug>.md` (meta-repo) or `<project>/docs/decisions/ADR-NNN-<slug>.md` (downstream) | One ADR per significant decision. Reserve NNN in `INDEX.md` first (per ADR-009). |
| **Retros** | `~/Projects/cascade-system/retros/sprint-N.md` (meta-repo) or `<project>/docs/retros/<milestone-slug>.md` (downstream) | Immutable once `Status: closed` (per ADR-011). |
| **Handoffs** | `~/Projects/cascade-system/docs/handoffs/<cascade-id>-<scope>.md` | Status changes via INDEX.md update; file content stays. |

### Paths that are dead

- `~/.windsurf/skills/`, `~/.windsurf/workflows/`, `~/.windsurf/rules/` → Windsurf does not scan them. The migration on 2026-04-30 (ADR-014) emptied these. Anything authored there will be invisible to Cascade.

### Cross-agent fallback paths (informational only)

Windsurf also discovers skills under `~/.agents/skills/` and `.agents/skills/` for cross-agent compatibility (and `~/.claude/skills/`, `.claude/skills/` if Claude Code config reading is enabled). We do **not** use these as primary — `~/.codeium/windsurf/` is canonical.

## How to update an L1 component

Always route through `@update-horizontal`. Never edit L1 from outside that skill (exception: initial milestone authoring or `@write-skill` for new skills).

For rule edits specifically, the skill enforces dual-update: long-form archive in `~/Projects/cascade-system/docs/rules/<name>.md` AND the corresponding section in `~/.codeium/windsurf/memories/global_rules.md`. The two are siblings, not source/derived. Run `wc -c ~/.codeium/windsurf/memories/global_rules.md` after every rule edit; must remain ≤6000.

For verification: invoke `@verify-l1` to confirm the layout is consistent before declaring an L1 change complete.

## How to start a new project

```
/start-project [<name> <type>]
```

The workflow walks 14 steps (validate type → scaffold → copy global rules → GitHub repo → initial commit → Project v2 → milestones → brainstorm phase → handoff). See `~/.codeium/windsurf/global_workflows/start-project.md` for the procedure.

Recommended: hand off to a fresh Cascade after `/start-project` completes — running phases in a context-cleared session prevents state bleed.

## How to run a phase

```
/run-phase <phase-name>
```

Looks up the phase in the project's `.windsurf/phases.yaml`, runs pre-checks, invokes the phase's skill, runs post-checks, hints at the next phase. Contract source: `~/.windsurf/contracts/phase-taxonomy.md`.

## Sprint lifecycle

1. Sprint plan locked into `~/.windsurf/plans/<sprint-slug>.md` (or amended per ADR if mid-sprint changes).
2. Phases execute via `/run-phase` per project `phases.yaml`.
3. Drift detected by `plan-drift-watcher` rule → `/recalibrate` workflow.
4. Milestone closes → `sprint-review-prompt` rule fires → `@sprint-review` skill drains the learning queue.
5. Approved L1 promotions hand off to `@update-horizontal`.
6. Retro closes (with `git status --porcelain` clean — hard gate per ADR-008).

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `@skills:` shows nothing | Skills authored at deprecated `~/.windsurf/skills/` instead of canonical `~/.codeium/windsurf/skills/` | Move them. Run `@verify-l1`. |
| Slash command not in list | Workflow missing `description:` frontmatter, or authored at deprecated path (`~/.codeium/windsurf/workflows/`) instead of `~/.codeium/windsurf/global_workflows/` (per ADR-016) | Add frontmatter or move file to `global_workflows/`. |
| Global rule "not taking effect" | Section missing from `global_rules.md`, OR file exceeds 6000 chars (silent truncation) | `wc -c` the file; ensure rule has a `## <name>` section. |
| Per-project rule out of date | Global rule edited after `/start-project` ran; per-project copy is stale | Manual `cp` from archive, or run `/sync-rules` (planned). |
| Cascade ignored a rule | Rule is `model_decision`-triggered and the description didn't match the conversation context | Either rewrite the description for better activation OR promote to `always_on`. |

## Pointers

- ADR-014 — the migration record for the L1 canonical paths
- ADR-016 — workflow canonical path correction (`global_workflows/`)
- ADR-015 — skill vs workflow invocation syntax (`@` vs `/`)
- ADR-006 — skill frontmatter schema
- ADR-003 — strict docs structure
- ADR-008 — clean working tree gate before closing retros
- ADR-011 — record retention (supersession over deletion)
- ADR-013 — `/commit` workflow as forcing function
- `docs/rules/INDEX.md` — the long-form rule archive index
- `docs/decisions/INDEX.md` — all ADRs
- `~/.codeium/windsurf/memories/global_rules.md` — the LAW (concise, always-on)

## Sections deferred

Mental model overview, vertical-spawning conventions, and full release discipline are deferred to a later authoring pass. Capture follow-ups in `cascade-system/queue/pending-review.md`.
