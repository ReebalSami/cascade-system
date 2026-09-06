---
trigger: always_on
description: Devin CLI and Windsurf both read global skills from `~/.codeium/windsurf/skills/`; that is the single content home for every L1 procedure (former workflows included — invoked as `/name` in Devin, `@name` in Windsurf). `~/.codeium/windsurf/global_workflows/` holds redirect stubs only. Consumer projects use `.devin/rules/`, `.devin/phases.yaml`, `.agents/skills/`. Never author L1 components under `~/.windsurf/` — that path is invisible to both tools. Every rule file declares `trigger:` explicitly.
sources_consulted:
  - cascade-system/docs/decisions/ADR-014-l1-canonical-storage-paths.md (own) — migration record that established the canonical paths
  - cascade-system/docs/decisions/ADR-016-workflow-canonical-path-correction.md (own) — corrects the workflow path from `workflows/` to `global_workflows/`
  - cascade-system/docs/decisions/ADR-037-devin-runtime-adoption-cluster.md (own) — workflows re-homed as skills; `global_workflows/` = stubs; consumer paths `.devin/` + `.agents/`; `trigger:`-required clause; strict-YAML frontmatter finding
  - https://docs.windsurf.com/llms-full.txt chunks 348,353,363 (upstream — skill and workflow storage location tables)
  - Devin CLI docs on disk (`/Applications/Devin.app/Contents/Resources/app/extensions/windsurf/devin/share/devin/docs/`) — `reference/configuration/read-config-from.mdx` (workflows not imported), `extensibility/skills/overview.mdx` (skill discovery paths, `triggers`), `extensibility/rules.mdx` (`.devin/rules/` honours `trigger:`)
adapted_for:
  - L1 global rule (always-on meta-rule about where L1 itself lives and how each tool loads it)
  - This long-form exists for completeness of the archive; the canonical path table is in ADR-014 + ADR-016 + ADR-037
  - Kept intentionally brief because the full rationale lives in the ADRs
---

# L1 canonical paths

The authoritative path table for all L1 components. This rule's purpose is to ensure every session — Devin or Windsurf — has the correct paths top-of-mind.

## Canonical paths (per ADR-014 + ADR-016 + ADR-037)

| Component | Canonical global path | Notes |
|---|---|---|
| **Skills** (incl. former workflows) | `~/.codeium/windsurf/skills/<name>/SKILL.md` | Multi-file. Read by Devin and Windsurf. Invoked `/<name>` (Devin) or `@<name>` (Windsurf). Deterministic procedures carry `triggers: [user]` + `activation: manual`. Frontmatter must parse as strict YAML — Devin silently drops a skill whose frontmatter does not parse. |
| **Workflow stubs** | `~/.codeium/windsurf/global_workflows/<name>.md` | 3-line redirect stubs so Windsurf's `/<name>` still resolves. **No procedures live here** (ADR-037). Devin does not read this directory. |
| **Rules (concise/active)** | `~/.codeium/windsurf/memories/global_rules.md` | Single file, ≤6,000 chars, always-on in both tools. The LAW. |
| **Rules (long-form/reference)** | `~/Projects/cascade-system/docs/rules/<name>.md` | Full frontmatter + rationale. Not auto-loaded. Every file declares `trigger:`. |
| **Rules (per-project)** | `<project>/.devin/rules/<name>.md` | Only the six `model_decision` long-forms + `strict-docs-placement` + L3 rules (deployed by `/start-project` step 6a/6b). Devin honours `trigger:`; legacy `.windsurf/rules/` is still read. |
| **Phases (per-project)** | `<project>/.devin/phases.yaml` | Runtime source of truth for `/run-phase`. Readers fall back to legacy `.windsurf/phases.yaml`. |
| **Skills (per-project)** | `<project>/.agents/skills/<name>/SKILL.md` | Cross-tool Agent Skills path — read by Devin, Claude Code, Cursor, Codex, Copilot; target of `npx skills add`. Not read by Windsurf (accepted trade-off, ADR-037). |
| **Contracts** | `~/.windsurf/contracts/<name>.md` | Agent-internal; NOT tool-loaded. Consumed by skills explicitly. |
| **Templates** | `~/.windsurf/templates/{<type>,_shared}/` | Agent-internal; consumed by `/start-project`. `_shared/scaffold/` ships `.devin/rules/strict-docs-placement.md`, `AGENTS.md`, `CLAUDE.md`. |

## Deprecated paths (do not use)

- `~/.windsurf/skills/`, `~/.windsurf/workflows/`, `~/.windsurf/rules/` — neither tool scans these (ADR-014)
- `~/.codeium/windsurf/workflows/` — wrong path; `global_workflows/` is the stub location (ADR-016)
- `~/.codeium/windsurf/global_workflows/` **for new content** — Devin does not import workflows; author a skill with `triggers: [user]` instead (ADR-037)
- `<project>/.windsurf/{rules,skills,workflows,phases.yaml}` **for new bootstraps** — consumer paths are `.devin/` + `.agents/` (ADR-037); readers keep a legacy fallback for pre-ADR-037 projects

## Loading facts that shape authoring

- **Every rule file declares `trigger:` explicitly** (`always_on`, `model_decision`, `manual`, `glob`, `agent`). A `description`-only rule file is treated as agent-decided by Devin and as always-on by nothing — the same file behaves differently across tools depending on whether `trigger:` is present (observed on portfolio-website's `.devin/rules/`, 2026-09-05). `@verify-l1` step 6 checks this.
- **Skill frontmatter must be strict YAML.** Quote any scalar containing `: ` or starting with a reserved indicator (`` ` ``, `@`, `*`, `&`, `!`, `%`, `|`, `>`, `#`). Six L1 skills were invisible to Devin for several sprints before M4A.1 caught this; `@verify-l1` step 3 parses every frontmatter block and cross-checks `devin skills list`.
- **Manual skills** (former workflows) set `triggers: [user]` (Devin never auto-invokes) and a `description` starting with "Manual: …" (Windsurf never auto-matches).

## Provenance

Long-form rationale, migration records, and alternatives considered: ADR-014, ADR-016, ADR-037 in `~/Projects/cascade-system/docs/decisions/`.

This rule was added to the archive during the Sprint 2 prep verification session as the `l1-canonical-paths` section existed in `global_rules.md` but had no corresponding archive file — a `@verify-l1` step 7 orphan detection finding. Rewritten at M4A.1 (2026-09-06) for the Devin runtime adoption (ADR-037); the concise section in `global_rules.md` was rewritten in the same change within the 6000-char cap.
