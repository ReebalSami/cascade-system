# Handoff: Cascade C — Obsidian integration (Sprint 2)

**From**: Cascade A (horizontal, Sprint 1 closer)
**To**: Cascade C (vertical, owns the Obsidian MCP integration + cross-project knowledge graph)
**Sprint**: 2 (Vertical C)
**Plan ref**: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 2 (M2C.1 – M2C.6)

You are a fresh Cascade picking up the meta-system's knowledge-integration vertical. Sprint 1 (L1 Foundation) is complete; this handoff is **self-contained** — disk + GitHub state + the user's Obsidian vault are your only inputs.

## 1 — Your role

You wire up the Obsidian MCP so Cascade-A authored skills can read and (where appropriate) write to the user's personal knowledge vault. Goal: cross-project notes (research log, thesis notes, paper summaries, brainstorm spillover) become first-class citizens of the system — `/grill-me`, `/recalibrate`, `/to-prd` can all reach into Obsidian for context.

You do **not** modify L1 (`~/.windsurf/`) outside of adding the Obsidian-aware MCP wiring + (if absolutely necessary) one or two new rules / skill updates that touch Obsidian-specific behavior. Even those are first proposed via the queue → Cascade A's `/update-horizontal` route, **not** authored directly by you.

You are vertical, not horizontal. Stay in scope.

## 2 — Read these in order before doing anything

| # | Path | Why |
|---|---|---|
| 1 | `~/Projects/cascade-system/README.md` | Top-level orientation |
| 2 | `~/Projects/cascade-system/docs/structure.md` | Meta-repo conventions |
| 3 | `~/Projects/cascade-system/retros/sprint-1.md` | What just shipped + open observations |
| 4 | `~/Projects/cascade-system/docs/decisions/INDEX.md` | All ADRs |
| 5 | `~/.windsurf/contracts/phase-taxonomy.md` | Note `obsidian://` artifact path scheme is reserved (M1.3 §4) |
| 6 | `~/.windsurf/rules/*.md` | All L1 rules (you operate under these) |
| 7 | `~/.windsurf/skills/grill-me/SKILL.md` | Reads context from Obsidian when enabled (M1.2 §1) |
| 8 | `~/.windsurf/skills/to-prd/SKILL.md` | Reads linked vault notes (M1.6) |
| 9 | `~/.windsurf/workflows/recalibrate.md` | Future drift signal: vault notes vs PRD |
| 10 | The user's Obsidian vault path (ask the user; not hardcoded) | Your operating surface |

## 3 — Work to do (Sprint 2 Vertical C)

Per plan §4 Sprint 2:

- **M2C.1** — Discover and configure an Obsidian MCP. Candidates: `mcp-obsidian`, `obsidian-mcp-server`, or build a minimal one. Verify it can: read note by path, search vault by tag/text, list backlinks, optionally write notes. Document the choice in an ADR (`ADR-NNN-obsidian-mcp-selection.md`).

- **M2C.2** — Define a vault layout convention for system-aware notes:
  - Where do thesis-related notes live? (e.g., `<vault>/02-projects/thesis/`)
  - How are project-linked notes tagged? (e.g., `#project/thesis`, `#cascade-aware`)
  - How does a Cascade know which notes apply to its active project?

- **M2C.3** — Update the L1 skills that have Obsidian touchpoints (these are owned by Cascade A; you propose changes via queue → `/update-horizontal`):
  - `/grill-me` — read linked vault notes during context load
  - `/to-prd` — incorporate vault findings into §13 Sources
  - `/recalibrate` — surface drift between vault and PRD as a 7th drift signal

- **M2C.4** — Author a new L1 rule (proposed via queue): `obsidian-context-priming` — when active project's `phases.yaml` has any `obsidian://` artifact path, prime the conversation with relevant vault notes at session start.

- **M2C.5** — Build a "vault → PRD" auxiliary skill (or extend `/to-prd`): when a project starts and the user has substantial existing vault notes on the topic, surface them at brainstorm time.

- **M2C.6** — Write the vertical retro at `~/Projects/cascade-system/retros/sprint-2-vertical-c.md`. Cover what shipped, vault layout decisions, MCP choice rationale, friction.

Each milestone closes its corresponding GitHub issue (filed during Sprint 0 M0.6 — check Project board for the M2C.x issues).

## 4 — Operating constraints

You operate under all L1 rules in `~/.windsurf/rules/`. Highlights for your work:

- **`adapt-from-all`**: research the Obsidian MCP space (HuggingFace, GitHub, awesome-mcp lists) before building anything custom. Cite consultation in any ADR.
- **`document-as-you-go`**: ADR for MCP selection, ADR for vault layout convention, ADR for any rule additions.
- **L1 changes go through the queue** → Cascade A's `/update-horizontal`. You write proposals; you don't apply L1 directly. Even small additions like `obsidian-context-priming` rule must route via Cascade A or the next horizontal `/sprint-review`.
- **`no-time-estimates`**, **`no-quantity-over-shape`**, **`no-terminal-oneline-scripts`**, **`make-sure-it-works`**, **`be-honest-direct-critical`** all apply.
- **Privacy**: the user's vault contains personal notes. Read carefully; don't dump unrelated notes into PRDs or chat. Surface only what's directly relevant to the active project.
- **`bidirectional-learning-pipe`**: capture every cross-project insight (those are the whole point of this vertical) to the queue.

ADR routing per ADR-001 still applies for any GitHub mutations you do (issue close + comment via MCP; project mutations via `gh`).

## 5 — Useful starting points

- HuggingFace MCP space search: `mcp4_space_search` with query "obsidian" and `mcp=true`
- Web search: "obsidian MCP server" — landscape is moving; expect 2-3 viable options
- The user's existing vault (path provided at session start) — explore non-destructively with grep/find first
- Existing reference repos in `~/Projects/cascade-system/refs/` — none are Obsidian-specific, but pattern conventions for SKILL.md / rules carry over

## 6 — Pitfalls to avoid (carried from Sprint 1 friction)

- **Don't author L1 directly** — propose via queue. Cascade A's `/update-horizontal` is the L1-mutation channel.
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
- L1 observations are in the queue for Cascade A's next horizontal `/sprint-review`

## 8 — Termination + handoff

When done, your last action is:

> Vertical C (Obsidian integration) complete. MCP: <name>. Retro at `retros/sprint-2-vertical-c.md`. L1 proposals queued for Cascade A's next horizontal `/sprint-review`. Cascades B and D can now use `obsidian://` artifact paths in their projects' `phases.yaml`.

Do **not** start Cascade D's work yourself. That's a separate vertical with its own handoff.

## 9 — Provenance

Pattern derived from `cascade-system/docs/decisions/ADR-002-handoff-prompts-subdirectory.md`. Sister handoff: `cascade-b-template-python-ml-uv.md`.

Sprint 1 retro: `retros/sprint-1.md`.
Plan: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 2 Vertical C.
