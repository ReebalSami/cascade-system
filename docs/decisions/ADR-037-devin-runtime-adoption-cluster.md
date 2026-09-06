# ADR-037: Devin runtime adoption cluster — workflows re-homed as skills, `.devin/` + `.agents/` consumer paths, rule-copy fix

**Status**: Proposed (apply at M4A.1; flip to Accepted when `@verify-l1` is clean and a fresh Devin session runs `/commit` + `/start-project --dry-run`)

**Date**: 2026-09-05

**Source**: `docs/prompts/stages/02-brainstorm-nextjs-marketing-site.md` §F1–F2, §C3–C8; Devin CLI on-disk docs; queue entry 2026-08-25 (HORUS `.windsurf/` → `.devin/` dead-path finding)

**Context**:

The system was designed for Windsurf Cascade (ADR-014/015/016). The user now runs Devin CLI as the primary agent runtime, with Windsurf still installed. Verified against the Devin docs shipped with the app (`/Applications/Devin.app/Contents/Resources/app/extensions/windsurf/devin/share/devin/docs/`):

1. **Workflows are not imported.** `reference/configuration/read-config-from.mdx` line 41: *"Windsurf workflows (`.windsurf/workflows/` and `~/.codeium/<channel>/global_workflows/`) are **not** imported as skills."* Consequence: `/start-project`, `/add-project-type`, `/run-phase`, `/recalibrate`, `/commit`, `/branch-start`, `/branch-push-and-pr`, `/ci-watch`, `/branch-merge-and-cleanup`, `/issue-create` do not exist in a Devin session. Every forcing function the rule set depends on (`/commit` for ADR-013, the branch helpers for ADR-018, `/issue-create` for ADR-036) is silently absent.
2. **Skills are slash commands.** `extensibility/skills/overview.mdx`: users invoke skills with `/skill-name`; `triggers: [user]` prevents model auto-invocation. Global skills are read from `~/.codeium/<channel>/skills/` (channel-dependent), `~/.config/devin/skills/`, `~/.agents/skills/`; project skills from `.agents/skills/`, `.devin/skills/`, `.windsurf/skills/`. Unknown frontmatter keys are tolerated — the existing L1 skills (with `activation`, `sources_consulted`, `adapted_for`) load today. *Correction at apply time (M4A.1, 2026-09-06): only 11 of 17 loaded. See Amendment below.*
3. **Rules.** `.devin/rules/*.md` and `.windsurf/rules/*.md` are both loaded and honour `trigger:` (`always_on`, `manual`, `model_decision`, `agent`, `glob`); `.devin/` is preferred. `~/.codeium/windsurf/memories/global_rules.md` is loaded. Devin's guidance: *"Rules and AGENTS should be kept as small as possible"*; use skills for anything on-demand.
4. **Rule duplication.** `/start-project` step 6a copies every long-form rule from `cascade-system/docs/rules/` (21 files: 15 `trigger: always_on`, 6 `model_decision`) into the project's rules directory. Under Windsurf this was "re-readable long-form" (ADR-014). Under Devin, the 15 `always_on` copies are injected every session on top of `global_rules.md`, which already carries their concise versions — roughly double the rule tokens per session, against ADR-033 and Devin's own guidance.
5. **Dead paths.** `<project>/.windsurf/…` is hard-coded in 12 active L1 files (`start-project`, `run-phase`, `verify-l1`, `grill-me`, `kickoff`, `begin`, `docs-refresh`, `propose-extension`, `to-issues`, `sync-github`, `write-skill`, `update-horizontal`) and 7 template/contract files (`phase-taxonomy.md` §2, `_shared/scaffold/.windsurf/rules/strict-docs-placement.md`, `_shared/scaffold/docs/{structure.md,decisions/INDEX.md}`, `python-ml-uv/rules/{config-discipline,notebook-discipline}.md`, `python-ml-uv/scaffold/README.md`). HORUS and portfolio-website already renamed to `.devin/`; the 2026-08-25 queue entry documents 17 dead references that rename produced in HORUS.
6. **Ecosystem direction (research 2026-09-05).** Claude Code merged `commands/` into skills (v2.1.3, 2026-01-09); Cursor retired `.cursor/commands/` behind `/migrate-to-skills`; the Agent Skills open standard (agentskills.io; `.agents/skills/`; SKILL.md + `references/` + `scripts/`) is supported by Devin, Claude Code, Cursor, Codex, Copilot, Gemini CLI. No Devin built-in slash command collides with any of the 10 workflow names (`reference/commands.mdx`).

The `nextjs-marketing-site` template (M4E.x) cannot be authored (`/add-project-type`) or consumed (`/start-project`) in Devin until this is resolved. The user selected **Devin-first, Windsurf-compatible** as the runtime posture (brainstorm F1) and approved the L1 mutation bundle.

**Decision**:

Adopt a five-part cluster, applied in one coding-session milestone (M4A.1) as one PR:

1. **Workflows → skills.** Each of the 10 files in `~/.codeium/windsurf/global_workflows/` becomes `~/.codeium/windsurf/skills/<name>/SKILL.md` with frontmatter `name`, `description` (narrow, starting "Manual: …" so Windsurf's description-matching never auto-activates it), `triggers: [user]` (Devin manual-only), `activation: manual` (ADR-006), `argument-hint`, plus the existing `sources_consulted` / `adapted_for` provenance. Bodies are moved unchanged except for path edits under part 2. The original `global_workflows/<name>.md` files are **kept as 3-line redirect stubs** (frontmatter `description` + "Superseded by the `@<name>` skill (ADR-037); invoke it.") so Windsurf `/name` still resolves and nothing is deleted (ADR-011). ADR-006's frontmatter schema gains `triggers` (list) and `argument-hint` (string), both optional.
2. **Consumer path sweep.** `<project>/.windsurf/rules/` → `<project>/.devin/rules/`; `<project>/.windsurf/phases.yaml` → `<project>/.devin/phases.yaml`; `<project>/.windsurf/skills/` → `<project>/.agents/skills/` (the cross-tool Agent Skills path, natively read by Devin and the install target of `npx skills add`). Applied to the 12 L1 files and 7 template/contract files above. Readers (`run-phase`, `recalibrate`, `sprint-review`, `plan-drift-watcher`, `grill-me`, `kickoff`, `begin`) resolve `.devin/phases.yaml` first and fall back to legacy `.windsurf/phases.yaml`. `phase-taxonomy.md` §2 gets a changelog note — a location change, not a field change, so no `version` bump. `_shared/scaffold/.windsurf/` is renamed to `_shared/scaffold/.devin/`.
3. **`/start-project` step 6a** copies only the 6 `model_decision` long-form rules (`know-your-hardware`, `obsidian-context-priming`, `model-selection-advisor`, `context7-and-docs-first`, `plan-drift-watcher`, `sprint-review-prompt`) into `<project>/.devin/rules/`. The 15 `always_on` long-forms are **referenced** (`cascade-system/docs/rules/`), not copied — their concise versions are already always-on via `global_rules.md`. `strict-docs-placement` (shared, `always_on`) stays: it is project-flavoured and not in `global_rules.md`.
4. **Shared `AGENTS.md` + `CLAUDE.md`.** `_shared/scaffold/AGENTS.md` (generic, ≤ ~20 lines: repo layout pointer, `docs/structure.md`, `.devin/phases.yaml`, "rules are tiny — invoke skills") and `_shared/scaffold/CLAUDE.md` containing `@AGENTS.md`. Type-specific scaffolds may override `AGENTS.md` (ADR-004 last-wins; `/start-project` prints the override).
5. **Documentation amendments.** ADR-015's invocation table is superseded: skills are invoked as `/name` in Devin and `@name` in Windsurf; the workflow layer survives only as compatibility stubs. ADR-014/016 path rows are amended (skills canonical at `~/.codeium/windsurf/skills/`; `global_workflows/` = stubs; consumer paths per part 2). The concise `l1-canonical-paths` section in `global_rules.md` is rewritten to the new facts within the 6000-char cap (`wc -m` gate after the edit). `docs/cheat-sheet.md` (§1 skills count, §2 workflows → "compatibility stubs", §3 workspace-rule note, "Authoritative paths"), `docs/manual.md` invocation cheat-sheet, `docs/skills/INDEX.md`, `docs/workflows/INDEX.md`, and `@verify-l1` (allow-list + a dead-path check for `<project>/.windsurf/` in active L1 files) are updated in the same PR.

Verification gate for "Accepted": `devin skills list` shows the 10 re-homed skills; a fresh Devin session runs `/commit` and `/start-project --dry-run python-ml-uv-test python-ml-uv` successfully; Windsurf `/start-project` stub resolves to the skill; `wc -m ~/.codeium/windsurf/memories/global_rules.md` ≤ 6000; `@verify-l1` clean; `grep` for `<project>/.windsurf/` across active L1 files returns zero hits.

**Alternatives considered**:

- **Thin `run-workflow` wrapper skill** (one skill that reads `global_workflows/<name>.md` and executes it) — cheapest; keeps ADR-015 intact; but two mental models for one thing, awkward invocation (`/run-workflow start-project`), and it papers over a gap the ecosystem has already closed. Rejected.
- **Defer; bootstrap the three sites by hand** — fastest start; loses `/commit` crash safety, branch discipline, and issue→Project atomicity exactly when three repos start. Rejected.
- **Move global skills to `~/.config/devin/skills/` (Devin-native path)** — cleaner for Devin, invisible to Windsurf; contradicts F1. Rejected; `~/.codeium/windsurf/skills/` is read by both.
- **Keep copying all long-form rules into consumers** — status quo; doubles always-on rule tokens under Devin. Rejected.
- **Project skills at `.devin/skills/` instead of `.agents/skills/`** — Devin reads both; `.agents/` is the cross-tool standard and the `npx skills add` target, so own and third-party skills share one directory. Chosen `.agents/`. Known trade-off: Windsurf does not read `.agents/skills/` (nor `.devin/`); global L1 skills remain dual-readable.
- **Bump the phase-taxonomy contract version** for the location change — the contract versions fields and schemes, not file locations; readers gain a fallback instead. Rejected.

**Consequences**:

- All 10 procedures become invocable in Devin under their existing names; Windsurf users see the same names via stubs. One content home → zero drift.
- Every future consumer bootstraps with `.devin/rules/` (6 on-demand rules + `strict-docs-placement`), `.devin/phases.yaml`, `.agents/skills/`, `AGENTS.md`, `CLAUDE.md`. Per-session rule tokens drop roughly by half versus the previous step 6a.
- Existing consumers that already carry 15 `always_on` long-form copies (portfolio-website `.devin/rules/` has description-only files and is unaffected; HORUS removed its copies at submission) are not swept by this ADR — a queue entry tracks a one-off sweep at the next horizontal `@sprint-review`.
- ADR-015 is superseded in its invocation table only; its skill-vs-workflow *design* distinction (judgment-heavy vs deterministic) survives as `triggers: [user]` + `activation: manual` on the converted skills.
- `global_rules.md` stays under cap by rewriting, not adding. No new always-on rule is introduced.
- `@propose-extension`'s route table gains no new artifact type: "Workflow (L1)" now routes to a skill with `triggers: [user]`; the row text is updated in the same PR.
- Follow-up (ADR-038) extends the re-homed `/start-project` with scaffold-time enhancements; this ADR deliberately does not bundle them.

**Amendment (M4A.1 apply, 2026-09-06 — `ReebalSami/cascade-system#114`)**:

Kickoff orientation for M4A.1 ran `devin skills list` and found **11 of 17** L1 skills, not 17. The six missing — `grill-me`, `to-prd`, `docs-refresh`, `vault-distill`, `verify-l1`, `write-skill` — had frontmatter that fails a strict YAML parse (`ruby -ryaml`): unquoted list items or descriptions containing `: ` (colon-space) or a value beginning with a backtick (a YAML reserved indicator). Devin drops such a skill silently; nothing surfaced the loss because `@verify-l1` was itself one of the six. Context ¶2's claim that the existing skills "load today" was therefore true for the eleven that happened to be well-formed. Windsurf behaviour for the six is unknown (not testable from a Devin session).

Folded into this ADR's apply (user decision at kickoff, fold-into-M4A.1 over a separate hotfix PR):

- **1b. Frontmatter fix** — the six offending scalars are double-quoted; content unchanged. Gate: every `~/.codeium/windsurf/skills/*/SKILL.md` frontmatter parses; `devin skills list` (run from outside the skills directory — the CLI is cwd-sensitive) lists every directory. The same gate caught three of the newly authored skill frontmatters in this apply before they shipped.
- **ADR-006 amendment** — frontmatter must be strict YAML; quote scalars containing `: ` or starting with `` ` `` `@` `*` `&` `!` `%` `|` `>` `#`. `@write-skill` step 8 and `@verify-l1` step 3 enforce it; `@verify-l1` also cross-checks `devin skills list` against the directory listing.
- **`l1-canonical-paths` long-form** gains a "Loading facts" section (strict YAML; explicit `trigger:` on every rule file; manual-skill conventions).
- Provenance cites in swept skills that pointed at the never-valid `cascade-system/.windsurf/rules/…` path now point at `cascade-system/docs/rules/…`.

Also applied in the same PR, discovered during the sweep: `_shared/scaffold/` files cited the ADR-014-dead `~/.windsurf/rules/…` path (pre-existing `@verify-l1` step-8 drift, never run); `docs/skills/INDEX.md` lacked four skills (`begin`, `kickoff`, `handoff-to-coding-session`, `handoff-to-thinking-session`); `@write-skill` step 9 still said "no global index file yet" (queue entry M_C2.3) — corrected since this apply adds ten L1 skills and must update the indexes it claimed did not exist.

**Source refs**:

- `docs/prompts/stages/02-brainstorm-nextjs-marketing-site.md` (M4E.1) — §F1, §F2, §C3–C10, §Carry-forward M4A.1
- Devin CLI docs (on disk): `reference/configuration/read-config-from.mdx`, `extensibility/skills/overview.mdx`, `extensibility/skills/creating-skills.mdx`, `extensibility/rules.mdx`, `reference/commands.mdx`
- `~/.codeium/windsurf/global_workflows/start-project.md` steps 5–6 (rule-copy loop being fixed)
- `~/.windsurf/contracts/phase-taxonomy.md` §2, §6
- ADR-006, ADR-011, ADR-014, ADR-015, ADR-016, ADR-018, ADR-033, ADR-036 (precedents amended or relied upon)
- `queue/pending-review.md` — 2026-08-25 HORUS dead-path entry; Sprint 1 M-2 L1-storage entry (unchanged by this ADR)
- Research pass 1 (agent-skills ecosystem, 2026-09-05): agentskills.io specification + client list; Claude Code release v2.1.3; Cursor skills docs; Vercel "AGENTS.md outperforms skills in our agent evals" (2026-01-27)
