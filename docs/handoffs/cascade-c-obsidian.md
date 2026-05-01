# Handoff: Cascade C — Obsidian integration (Sprint 2)

**From**: Cascade A (horizontal, Sprint 1 closer)
**To**: Cascade C (vertical, owns the Obsidian MCP integration + cross-project knowledge graph)
**Sprint**: 2 (Vertical C)
**Plan ref**: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 2 (M2C.1 – M2C.6)

You are a fresh Cascade picking up the meta-system's knowledge-integration vertical. Sprint 1 (L1 Foundation) is complete; this handoff is **self-contained** — disk + GitHub state + the user's Obsidian vault are your only inputs.

## 1 — Your role

You wire up the Obsidian MCP so Cascade-A authored skills can read and (where appropriate) write to the user's personal knowledge vault. Goal: cross-project notes (research log, thesis notes, paper summaries, brainstorm spillover) become first-class citizens of the system — `@grill-me`, `/recalibrate`, `@to-prd` can all reach into Obsidian for context.

You do **not** author L1 directly. The L1 active surface per ADR-014 is `~/.codeium/windsurf/` (skills / global_workflows / memories/global_rules.md) plus `~/.windsurf/` (contracts / templates) — neither is yours to edit. Per ADR-017, every L1 proposal (new rule, skill edit, workflow edit, template change, AGENTS.md edit) routes through `@propose-extension`, which dispatches to `@write-skill` / `@update-horizontal` / direct-write per its route table. Your touchpoint on L1 is authoring well-shaped proposals; horizontal Cascade A approves and applies them via `@sprint-review` → `@update-horizontal`.

You are vertical, not horizontal. Stay in scope.

## 2 — Read these in order before doing anything

| # | Path | Why |
|---|---|---|
| 1 | `~/Projects/cascade-system/AGENTS.md` | Auto-loaded on workspace open; start-here orientation + invocation cheat-sheet pointer (added post-Sprint-1) |
| 2 | `~/Projects/cascade-system/docs/manual.md` | **All sections** — especially "Mental model", "Spawning a vertical Cascade", "Release discipline", "Invocation cheat-sheet" (authored post-Sprint-1 per ADR-018 Phase B) |
| 3 | `~/Projects/cascade-system/README.md` | Top-level orientation |
| 4 | `~/Projects/cascade-system/retros/sprint-1.md` | What shipped in Sprint 1 + open observations |
| 5 | `~/Projects/cascade-system/docs/decisions/INDEX.md` | All ADRs — pay attention to ADR-014 (L1 canonical paths), ADR-015 (`@` vs `/` syntax), ADR-016 (workflow path correction), **ADR-017** (`@propose-extension` — your mandatory L1-proposal channel), **ADR-018** (`@release-manager` — your mandatory main-bound-commit channel) |
| 6 | `~/.windsurf/contracts/phase-taxonomy.md` | `obsidian://` artifact path scheme is reserved (M1.3 §4) |
| 7 | `~/Projects/cascade-system/docs/rules/*.md` | Long-form L1 rule archive (you operate under the concise versions in `global_rules.md`; read archive for rationale) |
| 8 | `~/.codeium/windsurf/skills/grill-me/SKILL.md` | Reads context from Obsidian when enabled (M1.2 §1) |
| 9 | `~/.codeium/windsurf/skills/to-prd/SKILL.md` | Reads linked vault notes (M1.6) |
| 10 | `~/.codeium/windsurf/global_workflows/recalibrate.md` | Future drift signal: vault notes vs PRD |
| 11 | `~/Projects/cascade-system/queue/pending-review.md` | Open learning queue; read-only (drain is `@sprint-review`'s job, not yours) |
| 12 | `~/.codeium/windsurf/mcp_config.json` | `obsidian-mcp-server` is pre-registered but `disabled: true` — M2C.1 evaluates and enables (or swaps for a better MCP) |
| 13 | The user's Obsidian vault path (ask the user; not hardcoded) | Your operating surface |

## 3 — Work to do (Sprint 2 Vertical C)

Per plan §4 Sprint 2:

- **M2C.1** — Discover and configure an Obsidian MCP. Candidates: `mcp-obsidian`, `obsidian-mcp-server`, or build a minimal one. Verify it can: read note by path, search vault by tag/text, list backlinks, optionally write notes. Document the choice in an ADR (`ADR-NNN-obsidian-mcp-selection.md`).

- **M2C.2** — Define a vault layout convention for system-aware notes:
  - Where do thesis-related notes live? (e.g., `<vault>/02-projects/thesis/`)
  - How are project-linked notes tagged? (e.g., `#project/thesis`, `#cascade-aware`)
  - How does a Cascade know which notes apply to its active project?

- **M2C.3** — Update the L1 skills that have Obsidian touchpoints (these are owned by Cascade A; you propose changes via queue → `@update-horizontal`):
  - `@grill-me` — read linked vault notes during context load
  - `@to-prd` — incorporate vault findings into §13 Sources
  - `/recalibrate` — surface drift between vault and PRD as a 7th drift signal

- **M2C.4** — Author a new L1 rule (proposed via queue): `obsidian-context-priming` — when active project's `phases.yaml` has any `obsidian://` artifact path, prime the conversation with relevant vault notes at session start.

- **M2C.5** — Build a "vault → PRD" auxiliary skill (or extend `@to-prd`): when a project starts and the user has substantial existing vault notes on the topic, surface them at brainstorm time.

- **M2C.6** — Write the vertical retro at `~/Projects/cascade-system/retros/sprint-2-vertical-c.md`. Cover what shipped, vault layout decisions, MCP choice rationale, friction.

Each milestone needs a GitHub issue. **Sprint 0 M0.6 only filed Sprint 1 (M1.x) issues — M2C.x issues do NOT exist yet.** Your first real action (before executing M2C.1) is to open `M2C.1` through `M2C.6` as issues on `ReebalSami/cascade-system` and attach them to the `cascade-system roadmap` Project. Per ADR-001 routing: issue creation via `mcp3_issue_write` (method `create`); milestone creation + project-attachment via `gh` CLI with `GITHUB_TOKEN= GH_TOKEN=` prefix.

## 4 — Operating constraints

You operate under all L1 rules in `~/Projects/cascade-system/docs/rules/`. Highlights for your work:

- **`adapt-from-all`**: research the Obsidian MCP space (HuggingFace `mcp4_space_search`, GitHub search, awesome-mcp lists) before building anything custom. Cite consultation in any ADR's `sources_consulted`.
- **`document-as-you-go`**: ADR for MCP selection, ADR for vault layout convention, ADR for any rule additions. Reserve NNN in `INDEX.md` before authoring (per ADR-009).
- **`@propose-extension` is the single intake channel for ALL L1 proposals** (per ADR-017). Invoke it for every rule / skill-edit / workflow-edit / contract / template / `AGENTS.md` change you want to propose. Do NOT author L1 artifacts directly; `@propose-extension` dispatches to the right path (`@write-skill` / `@update-horizontal` / direct-write) and enforces the `adapt-from-all` + 3-test gate. Horizontal Cascade A's next `@sprint-review` decides promotion.
- **`@release-manager` is mandatory for every main-bound commit** (per ADR-018). Use `/branch-start <type>/obsidian-<slug>` for each unit of work. Helper workflows: `/commit` for commits with body content, `/branch-push-and-pr` for PR open, `/ci-watch` (will report "no CI configured; merge gate is manual review only" — that's fine, exit clean), `/branch-merge-and-cleanup` for squash-merge + cleanup. The `branch-and-pr-required` rule is always-on; never `git push origin main` directly outside cold-start.
- **`no-time-estimates`**, **`no-quantity-over-shape`**, **`no-terminal-oneline-scripts`**, **`make-sure-it-works`**, **`be-honest-direct-critical`** all apply.
- **Privacy**: the user's vault contains personal notes. Read carefully with `grep_search` / `find_by_name` to locate intent; don't dump unrelated notes into PRDs or chat. Surface only what's directly relevant to the active project.
- **`bidirectional-learning-pipe`**: capture every cross-project insight (those are the whole point of this vertical) to `cascade-system/queue/pending-review.md` with date + project + cascade tags.

ADR routing per ADR-001 still applies for any GitHub mutations you do (issue close + comment via MCP; project mutations via `gh`).

## 5 — Useful starting points

- HuggingFace MCP space search: `mcp4_space_search` with query "obsidian" and `mcp=true`
- Web search: "obsidian MCP server" — landscape is moving; expect 2-3 viable options
- The user's existing vault (path provided at session start) — explore non-destructively with grep/find first
- Existing reference repos in `~/Projects/cascade-system/refs/` — none are Obsidian-specific, but pattern conventions for SKILL.md / rules carry over

## 6 — Pitfalls to avoid (carried from Sprint 1 friction)

- **Don't author L1 directly** — propose via queue. Cascade A's `@update-horizontal` is the L1-mutation channel.
- **Don't dump vault content** — read with intent. The user's privacy expectations on personal notes are stronger than on code.
- **Don't ship an MCP that requires per-project setup** — vault MCP wiring should be one-time at L1 + per-project enable flag in `phases.yaml` (or `.windsurf/config.yaml` if needed; ADR if you create one).
- **Don't drift into Vertical B's scope** — `python-ml-uv` template is Cascade B. If you find yourself authoring `pyproject.toml` snippets, you've drifted.
- **Don't bundle MCP selection with vault layout** — they're separate ADRs. M2C.1 is "which MCP"; M2C.2 is "how does the vault organize Cascade-aware content". One decision per ADR.

## 7 — Definition of done

You're finished when:

- An Obsidian MCP is configured and demonstrably working (read + search at minimum)
- Vault layout convention is documented in an ADR
- L1 update proposals are queued (not yet applied)
- Auxiliary skill (M2C.5) is shipped at L2 or as an L1 proposal
- Sprint 2 vertical C retro is written and `Status: closed`
- All M2C.x issues are closed
- L1 observations are in the queue for Cascade A's next horizontal `@sprint-review`

## 8 — Termination + handoff

When done, your last action is:

> Vertical C (Obsidian integration) complete. MCP: <name>. Retro at `retros/sprint-2-vertical-c.md`. L1 proposals queued for Cascade A's next horizontal `@sprint-review`. Cascades B and D can now use `obsidian://` artifact paths in their projects' `phases.yaml`.

Do **not** start Cascade D's work yourself. That's a separate vertical with its own handoff.

## 9 — Provenance

Pattern derived from `cascade-system/docs/decisions/ADR-002-handoff-prompts-subdirectory.md`. Sister handoff: `cascade-b-template-python-ml-uv.md`.

Sprint 1 retro: `retros/sprint-1.md`.
Plan: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 2 Vertical C.
