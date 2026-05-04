# ADR-023: Vault layout v3 (`_meta/raw/originals/wiki` spine) + iCloud-symlink ratification

**Status**: Accepted
**Date**: 2026-05-04
**Supersedes**:

- Parent plan `cascade-c-obsidian-kickoff-3bf063.md` §3–5 M2C.2 sequence (revised to in-place restructure per §10 re-design)
- Parent v2 plan `cascade-project-system-cac5f9.md` §4 M2C.2 *"copy canonical vault to `~/Projects/obsidian-vault/`"* (superseded by in-place restructure; vault path unchanged)
- Handoff `docs/handoffs/cascade-c-obsidian.md` §3 M2C.2 framing (generic "pick a layout convention" → replaced by this concrete v3 spine)

**Plan**: `~/.windsurf/plans/m2c2-vault-execution-kickoff-7afcc2.md` + parent `cascade-c-obsidian-kickoff-3bf063.md` §10 (design lock)
**Handoff**: `docs/handoffs/cascade-c-m2c2-vault-execution.md`
**Issue**: [#42](https://github.com/ReebalSami/cascade-system/issues/42) (M2C.2)

## Context

Sprint 2 Vertical C's M2C.2 milestone required a vault layout convention so Cascade-aware notes and system-aware tags have a deterministic home, and so that cross-project knowledge graph queries (per `@grill-me`, `@to-prd`, `/recalibrate`) have predictable shapes.

**Pre-restructure state** (discovered 2026-05-04):

- Vault at `~/Projects/obsidian/second-brain/` (iCloud-symlinked — see ADR-022 §4 for discovery + materialization implications)
- ~120 markdown files distributed across 10 legacy top-levels: `Inbox/`, `Knowledge Hub/`, `Personal/`, `Projects/`, `Archives/`, `University/`, `Meta/`, `✅ Kanban/`, `blogs/`, `Clippings/`, plus a root-orphan `Untitled 3.md`
- Mixed languages (German + English + Spanish), mixed content types (journal, research, course notes, LinkedIn drafts, recipes, idea backlog, web clippings)
- No schema file, no routing rules, no frontmatter contract, no LLM-vs-user ownership separation, no quality gate
- Structure was near-PARA but drifted over ~2 years of ad-hoc capture

**Design process** (2026-05-04 session `cascade-c-obsidian-kickoff-3bf063`):

- Surveys of PARA, Zettelkasten, Johnny Decimal, ACCESS, LYT (Nick Milo), and Karpathy's LLM wiki gist — per `adapt-from-all` rule
- 19-question grill-me interview (Q5–Q23, parent plan §10.1) walking the design tree
- Q22 locked the `_meta/AGENTS.md` 13-section draft
- Q23 locked the in-place migration approach (user directive: preserve vault name, one vault only, all content migrates; backup exists externally)
- Design declared LOCKED at session end; execution deferred to a fresh Cascade per user directive

**Execution** (2026-05-04, this session, handoff-driven):

- Phase 0: handoff promoted from `~/.windsurf/plans/` to `docs/handoffs/` via PR #48 (merged squash commit `f62ba0d`)
- Phase 1: v3 skeleton created (223 directories, 14 files — `_meta/AGENTS.md` verbatim + 8 templates + `wiki/` stubs)
- Phase 2: all 10 legacy top-level folders + 1 root-orphan file migrated or removed (118 files in v3 structure; legacy `Meta/`, `Personal/`, `Projects/`, `University/`, `Knowledge Hub/`, `Inbox/`, `Archives/`, `Clippings/`, `blogs/`, `✅ Kanban/`, `Untitled 3.md`)
- Phase 3: validation passed — smoke tests on Obsidian CLI index + wikilink resolution via `obsidian backlinks` + test journal entry round-trip

## Decision

**Adopt vault layout v3:**

```
second-brain/                    # iCloud-symlinked; name unchanged
├── _meta/                       # System: schema, templates, scripts, log, README
├── raw/                         # Inbound source-of-truth (immutable post-triage)
├── originals/                   # Curated life artifacts (user-authored)
└── wiki/                        # LLM-distilled durable knowledge
```

Sort order: `_meta/` → `originals/` → `raw/` → `wiki/` (underscore sorts above letters per Obsidian + Finder default).

### Layer ownership + mutability

| Layer | Owner | Mutability |
|---|---|---|
| `_meta/` | Cascade + Reebal (co-maintained) | Co-evolved via `@propose-extension` |
| `raw/` | Reebal (capture) + Cascade (triage) | Immutable post-triage |
| `originals/` | Reebal | Reebal mutates (Cascade never writes here without explicit instruction) |
| `wiki/` | Cascade | Cascade mutates (distilled knowledge; Reebal reads, rarely edits) |

### `originals/` — 7 domains (locked via grill-me Q8–Q18)

- `journal/` (daily, events, weekly, monthly, yearly — ISO-8601 naming, year sub-folders)
- `stories/` (written/{linkedin, blog, reflections}/{drafts, published} + spoken/{presentations, pitches, meetings, conversations})
- `academic/` (masters/{thesis, courses/<slug>, admin} + bachelors/ + further-education/<program>/ + research/)
- `software/` (projects/<repo>/ + learning-paths/ + infrastructure/ + ideas/)
- `languages/` (german, english, spanish — each with classes/practice/consumed/grammars/idioms/flashcards)
- `professional/` (career/{strategy, applications, interviews, certifications} + employers/ + clients/ + ventures/ + network/)
- `personal/` (sport, nutrition, cooking, music, travel, camper, finance, health — extensible: family/, home/, legal/, etc. added when needed)

### `wiki/` — Karpathy type-first ontology (locked via Q20)

- `entities/` — people/organizations/tools/places
- `concepts/` — domain-split (professional, software, academic, languages, personal, storytelling, crosscut) + per-domain sub-folders
- `sources/` — one card per ingested source (books, papers, articles, podcasts, videos, courses)
- `synthesis/` — cross-cutting analyses worth keeping
- `mocs/` — Maps of Content (LYT convention per Nick Milo; `MOC - home.md` = master, vault entry point)
- `index.md` — auto-maintained master index (Cascade writes)

### `raw/` — triage workflow (locked via Q21)

- `_inbox/` — transient capture (underscore prefix sorts top)
- `books/`, `papers/`, `clippings/`, `voice/`, `courses/` — triaged canonical locations (immutable after triage)

### `_meta/AGENTS.md` — load-bearing schema (locked via Q22)

13 sections: Architecture, Routing rules, `originals/` domain map, Naming conventions, Frontmatter contract, Cross-link rules, MOC convention, LLM operations (ingest/query/lint), Quality gate, Multilingual handling, Guardrails, Session start protocol, Co-evolution. Read FIRST every session before any vault operation.

### Migration approach (locked via Q23)

- **In-place restructure** (not greenfield archive + selective migration — user directive 2026-05-04)
- Vault name stays `second-brain/`; vault path unchanged
- ALL existing content migrates into v3 structure (~30–50 keepers rejected as under-scoped; all ~120 files placed)
- One vault only; backup exists externally (no dual-vault, no archive subdirectory within the vault)
- Filesystem mutations via `mv` at shell; Obsidian CLI `obsidian files` / `obsidian backlinks` verifies resolution post-migration

### iCloud-symlink ratification

Canonical access path: `~/Projects/obsidian/second-brain` → symlinked to `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain/` (symlink predates this ADR; discovered during M2C.1 pre-flight). Cascade interacts via the first-party `obsidian` CLI (v1.12.7+) per ADR-022, which queries the running app's in-memory index — iCloud materialization stubs are transparently handled by Obsidian itself, so the filesystem-direct MCP failure mode (silent 404 on 0-byte stubs) cannot occur with this architecture.

## Consequences

**Enables:**

- Deterministic routing: `@grill-me` / `@to-prd` / `/recalibrate` can cite `originals/academic/masters/thesis/` or `wiki/concepts/<domain>/<sub>/` as predictable targets instead of "somewhere in Obsidian"
- Quality gate at ingestion (AGENTS.md §9): Karpathy's load-bearing rule — Cascade declines low-signal / duplicate / untrustworthy / out-of-scope content rather than bloating `wiki/`
- Strict owner separation: Reebal-authored `originals/` and Cascade-authored `wiki/` cannot collide; cross-links via `linked_*:` frontmatter + `[[wikilinks]]` preserve connections without duplication
- Co-evolution via `@propose-extension`: schema updates become explicit ADRs in `_meta/decisions/` if material, otherwise direct PRs (mirrors `cascade-system` discipline)

**Constrains:**

- Any new content MUST route per AGENTS.md §2 — no silent "Knowledge Hub"-style catch-alls
- Cascade MUST NOT modify `raw/<books|papers|clippings|voice|courses>/` post-triage (immutable) or `originals/` without Reebal's explicit instruction (AGENTS.md §11)
- MOC convention: every MOC is `MOC - <topic>.md` at `wiki/mocs/` with `tags: [moc]` + H2 headings + `[[wikilinks]]` only (no inline content) — deviating creates backlink noise
- Git versioning NOT enabled initially (user directive; revisit later if multi-machine sync value emerges; mobile iCloud-sync is the current sync mechanism)

**Future extensibility**: new sub-domains under `originals/personal/` etc. are added as folders without schema changes — the spine is stable, leaves grow.

**Deferred** (captured in `_meta/log.md` Phase 2 completion + Phase 3 residuals):

- Frontmatter stamping on ~100 migrated files (content intact; per-file domain fields applied lazily as user touches each per §13 co-evolution)
- `raw/_inbox/kanban-all-backlog.md` per-item triage (idea backlog from legacy `✅ Kanban/`)
- `_meta/templates/legacy/` review (2 pre-existing course-folder Templater scripts)
- Papers in `originals/academic/masters/courses/seminar-safe-rl/papers/` normalization to `raw/papers/` canonical location with cross-link (one-canonical-home per AGENTS.md §6)
- Clipping ingestion to `wiki/sources/articles/` (3 Karpathy articles topically relevant to this ADR itself)

## Alternatives considered

| Candidate | Shape | Verdict |
|---|---|---|
| **PARA** (Tiago Forte) | Projects / Areas / Resources / Archives | Tried implicitly (pre-restructure had `Projects/`, `Personal/` resembling Areas, `Archives/`, `Knowledge Hub/` resembling Resources) — drifted because PARA lacks LLM-vs-user ownership separation and has no schema file |
| **Zettelkasten** (Luhmann) | Atomic notes + IDs + unique permalinks | Discipline cost too high for personal knowledge capture (user isn't writing academic papers daily); IDs don't add value over Obsidian's filename-based wikilinks |
| **Johnny Decimal** (Jeff Dance) | `10-19 Area / 11 Category / 11.01 Topic` | Enforces discovery discipline but forbids natural hierarchies (Sport/Bouldern/Routes is more natural than `20.31 Bouldern Routes`); LLM operations don't benefit from numeric ordering |
| **ACCESS** (Bryan Jenks) | Atlas / Calendar / Cards / Efforts / Sources / Spaces | Close to v3 but splits concepts from sources wrong direction for LLM ingest/query (sources are consumed to produce concepts; ACCESS treats them as peers) |
| **LYT** (Nick Milo) | Maps of Content + atomic notes + emergent structure | Adopted **partially** — LYT's MOC convention maps cleanly to `wiki/mocs/` (AGENTS.md §7); but MOC-only without structural spine led to drift in Milo's own published vaults |
| **Karpathy LLM wiki** (2026 gist) | `raw/` + `wiki/` + entities/concepts/sources types | Adopted as the **LLM-facing spine** — its type-first ontology (entities / concepts / sources / synthesis) is the most rigorous separation between "captured material" and "distilled knowledge" yet published; the "quality gate before storage" rule (AGENTS.md §9) comes straight from Karpathy |
| **Mattgiaro 4,819-note vault** | Second-brain PARA variant with daily-notes spine | Proof-of-scale for PARA-style, but no LLM ownership split; referenced for scale confidence not shape |
| **v3 (chosen)** | `_meta/` (schema) + `raw/` (capture) + `originals/` (user) + `wiki/` (LLM) | Hybrid — PARA-lite in `originals/` 7-domain split + Karpathy in `raw/` + `wiki/` + LYT MOCs + Johnny-Decimal-style `_meta/` prefix for system dir sort priority |

### Decisive criteria

- **LLM-vs-user ownership**: only Karpathy + v3 separate them cleanly; every other candidate mixes "notes I write" and "facts distilled from sources"
- **Extensibility without schema change**: v3 7-domain `originals/` + domain-split `wiki/concepts/` allow new folders without touching AGENTS.md; Johnny Decimal would force renumbering
- **Multilingual**: folder names must be English (§10); file content preserves source language; only v3 + Karpathy handle this explicitly
- **Discovery mechanism**: `wiki/index.md` + `MOC - home.md` + `obsidian://` URI grammar (reserved in M1.3 `phase-taxonomy` contract) — covers the issue #42 AC "discovery mechanism" line item
- **Tag scheme**: v3 uses frontmatter `domain:` + `sub_domain:` + `linked_*:` fields + MOC `tags: [moc]` — path is routing; tags are metadata (AGENTS.md §5)
- **Where do thesis-related notes live?** — `originals/academic/masters/thesis/` is canonical; per-project notes cross-link via `linked_academic:` / `linked_software:` (AGENTS.md §6) — covers issue #42 AC

## Sources consulted

**`adapt-from-all` rule — consulted during kickoff design phase (cascade-c-obsidian-kickoff-3bf063 §10):**

- Karpathy LLM wiki gist: `https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f` (chunks 5–9, 14–22 re-read mid-session)
- llmwiki-cli reference implementation: `https://github.com/doum1004/llmwiki-cli`
- Nick Milo LYT MOCs: `https://www.natecue.com/en/learn/productivity/map-of-content/`
- Mattgiaro 4,819-note vault: `https://mattgiaro.com/second-brain-obsidian/`
- PARA (Tiago Forte, Building a Second Brain, 2022)
- Zettelkasten (Luhmann method, surveyed via Sönke Ahrens *How to Take Smart Notes*)
- Johnny Decimal (johnnydecimal.com)
- ACCESS (Bryan Jenks, tfthacker + community posts)

**Related ADRs:**

- ADR-001 (MCP vs `gh` CLI routing) — N/A (no GitHub mutations in this ADR itself)
- ADR-002 (handoff-prompts subdirectory) — followed for PR-1 handoff promotion
- ADR-009 (NNN reserve-in-INDEX-first) — applied; row reserved before authoring
- ADR-011 (supersession over deletion) — applied to legacy vault content (soft-deletion = supersede-with-link; backup holds true deletions) + pre-existing handoff (`cascade-c-obsidian.md` status-updated not removed)
- ADR-017 (`@propose-extension` intake) — AGENTS.md §13 co-evolution routes through it
- ADR-018 (release-discipline cluster) — this ADR lands via two-PR shape: PR-1 (handoff promotion, merged as `f62ba0d`) + PR-2 (this ADR + INDEX update + closure)
- ADR-022 (Obsidian CLI selection) — enables the Cascade vault-access architecture this ADR assumes; iCloud-symlink ratification in this ADR depends on ADR-022's running-app-index access model

**Handoff reference:**

- `docs/handoffs/cascade-c-m2c2-vault-execution.md` — self-contained execution doc (Phase 0–4 + ADR-023 authoring + `@release-manager` flow); design locked in parent plan §10

**Plan references:**

- `~/.windsurf/plans/m2c2-vault-execution-kickoff-7afcc2.md` — this session's kickoff plan (two-PR shape)
- `~/.windsurf/plans/cascade-c-obsidian-kickoff-3bf063.md` §10 — design lock (archive-quality; do not modify)

## Evidence

**Post-migration validation (2026-05-04, this session)**:

| Check | Command | Result |
|---|---:|---|
| Vault root has exactly 4 v3 folders + `.obsidian/` | `ls "$V"` | ✓ `_meta/`, `originals/`, `raw/`, `wiki/` + `.obsidian/` |
| All 11 legacy top-level items removed (10 folders + 1 root orphan) | `ls "$V"` | ✓ none present |
| File count in v3 structure | `find "$V/{_meta,raw,originals,wiki}" -type f \| wc -l` | 118 |
| Obsidian CLI sees the vault | `obsidian vaults` | `second-brain` |
| Obsidian CLI indexes v3 files | `obsidian eval code='app.vault.getFiles().length'` | 117 (sync catching up) |
| AGENTS.md is the schema (not a stub) | `obsidian read file=_meta/AGENTS.md` | 164 lines, 13 sections |
| Wikilink resolution survives kebab-case rename | `obsidian backlinks file=wochenplan format=json` | 6 sport/routines/ files backlink correctly |
| Test journal note round-trips | create + `obsidian files` + delete | ✓ |
| Orphans (no inbound links) | `obsidian orphans total` | 107 (expected — MOC/cross-link authoring is ongoing) |

**Migration breakdown** (`_meta/log.md` for full audit trail):

- Skeleton: 223 dirs + 14 files (AGENTS.md + log.md + README.md + 8 templates + 2 wiki stubs + .gitkeep)
- 11 storytelling techniques split from single `Knowledge Hub/Storytelling Mastery/Untitled.md` into individual `wiki/concepts/storytelling/techniques/<slug>.md` files per handoff Phase 2 step 10
- 3 Karpathy clippings moved to `raw/clippings/` (topically relevant to this ADR itself)
- 1 legacy Kanban board → `raw/_inbox/` for user-triage (not deleted, per ADR-011 supersession)
- Legacy Meta Templater scripts preserved at `_meta/templates/legacy/`
- Empty placeholders deleted: `Andalusian.md`, `Schwimmen.md`, and empty-dir legacy containers (Masterarbeit/, General Resources/, Research/, KI-Methonden/)

## Notes

**Why two PRs for M2C.2** (per kickoff plan):

- PR-1: Phase 0 standalone handoff promotion (low-risk doc move) — merged as `f62ba0d`
- PR-2 (this ADR's PR): authoring + INDEX update + closure of issue #42

Rationale: vault work (Phase 1–3) is not git-tracked (per parent plan §10.7), so it runs between the two PRs without repo interaction. Clean PR boundaries; each PR has a single concern.

**Why NOT git-version the vault initially**:

Parent plan §10.7 user directive: iCloud-sync handles mobile + the vault isn't under multi-machine collaboration pressure yet. Deferred until value emerges (e.g., if Cascade starts running on a second machine or if rollback-from-mistake becomes a real risk).
