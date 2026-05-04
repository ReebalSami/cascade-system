# M2C.2 Vault Execution — Handoff for Fresh Cascade Session

**Date drafted:** 2026-05-04
**Drafted by:** Cascade C (kickoff conversation `cascade-c-obsidian-kickoff-3bf063`)
**For:** Fresh Cascade session picking up M2C.2 execution
**Issue:** [ReebalSami/cascade-system#42](https://github.com/ReebalSami/cascade-system/issues/42)
**Status when this handoff was written:** design COMPLETE; execution NOT started; user directed freeze + fresh-session pickup

---

## How to use this document

1. Invoke `@kickoff` and pass this file path
2. Read the **parent kickoff plan §10** (`~/.windsurf/plans/cascade-c-obsidian-kickoff-3bf063.md` lines 700+) for the full design lock — that section contains:
   - The complete v3 vault tree (`_meta/` + `raw/` + `originals/` + `wiki/`)
   - All 7 originals/ domain shapes (journal, stories, academic, software, languages, professional, personal)
   - The Karpathy-aligned wiki/ shape (entities/concepts/sources/synthesis/mocs)
   - The full 13-section `_meta/AGENTS.md` draft
   - User's per-top-level migration mapping (which old folder maps where)
   - Cross-link rules
3. Promote this file to `cascade-system/docs/handoffs/cascade-c-m2c2-vault-execution.md` as part of execution Phase 0
4. Execute Phase 1 → Phase 4 below in order
5. Author ADR-023 + close issue #42 + run `@release-manager`

## Lifecycle case for fresh session

**Mid-execution.** Design phase locked in parent plan §10. Vault in current chaos state. ADR-023 not yet authored. Issue #42 still open. M2C.3-M2C.6 untouched.

## Acceptance criteria (verbatim from issue #42)

- [ ] PARA / Zettelkasten / Johnny-Decimal / ACCESS survey cited in `sources_consulted` (✅ DONE in design phase — see parent plan §10.0 + chat history)
- [ ] Decision: where do thesis-related (and other project-linked) notes live? (✅ DONE — `originals/academic/masters/thesis/` for thesis; per-project notes in `originals/software/projects/<repo>/` cross-linked via `linked_academic:` / `linked_software:` frontmatter)
- [ ] Decision: tag scheme for Cascade-aware notes (✅ DONE — frontmatter `domain:` + `sub_domain:` + `linked_*:` fields; MOC tags `tags: [moc]`; no project-specific tags needed because routing is by path)
- [ ] Decision: discovery mechanism (✅ DONE — wiki/index.md + MOC - home.md + obsidian:// URI grammar via Obsidian CLI v1.12.7 per ADR-022)
- [ ] iCloud-symlink ratified as canonical access path (PENDING — write into ADR-023 §Decision)
- [ ] User-approval checkpoint before ADR-023 finalizes (PENDING)
- [ ] ADR-023 reserved in `INDEX.md` THEN authored (PENDING)
- [ ] PR via `@release-manager`; squash-merged (PENDING)

## Phase 1 — Skeleton (target: ~30 min)

**Goal:** Create v3 top-level structure at vault root alongside existing chaos. Don't touch existing folders yet.

Steps:
1. Verify access via Obsidian CLI: `obsidian status` (should show vault at iCloud path with symlink intact)
2. Create top-level v3 folders at vault root:
   - `_meta/`
   - `raw/`
   - `originals/`
   - `wiki/`
3. Create `_meta/` scaffolding:
   - `_meta/AGENTS.md` — write the 13-section draft VERBATIM from the markdown block immediately below (locked in Q22 of the kickoff grill-me, 2026-05-04). Do not paraphrase or summarize; write exactly as shown:

   ````markdown
   # Reebal's Vault — AGENTS.md

   Personal knowledge vault for Reebal Sami. Co-maintained: Reebal authors originals/raw,
   Cascade authors wiki/. Read this file FIRST every session before any vault operation.

   ## 1. Architecture (4 layers)

   | Layer        | Purpose                                                      | Owner          | Mutability         |
   |--------------|--------------------------------------------------------------|----------------|--------------------|
   | `_meta/`     | System: schema, templates, scripts, log, README              | Cascade+Reebal | Co-evolved         |
   | `raw/`       | Inbound source-of-truth (papers, books, clippings, voice)    | Reebal+Cascade | Immutable post-triage |
   | `originals/` | Curated life artifacts (journal, drafts, deliverables, logs) | Reebal         | Reebal mutates     |
   | `wiki/`      | LLM-distilled durable knowledge (entities/concepts/etc.)     | Cascade        | Cascade mutates    |

   ## 2. Routing rules (where new content goes)

   | Input                                                     | Goes to                                            |
   |-----------------------------------------------------------|----------------------------------------------------|
   | Voice memo / web clip / screenshot / untriaged text       | `raw/_inbox/`                                      |
   | Triaged source (paper, book, kept clipping, course)       | `raw/<books\|papers\|clippings\|voice\|courses>/`  |
   | Personal artifact authored by Reebal                      | `originals/<domain>/...`                           |
   | LLM source card (one per ingested source)                 | `wiki/sources/<type>/<slug>.md`                    |
   | LLM concept page                                          | `wiki/concepts/<domain>/<sub>/<concept>.md`        |
   | LLM entity page (person, org, tool, place)                | `wiki/entities/<type>/<name>.md`                   |
   | LLM cross-cutting analysis (Q&A result worth keeping)     | `wiki/synthesis/<slug>.md`                         |
   | Map of Content for a topic                                | `wiki/mocs/MOC - <topic>.md`                       |

   ## 3. Originals/ domain map (locked via grill-me, 2026-05-04)

   - `journal/` — daily/events/weekly/monthly/yearly (year sub-folders, ISO-8601 names)
   - `stories/` — written/{linkedin,blog,reflections} + spoken/{presentations,pitches,meetings,conversations}
   - `academic/` — masters/{thesis,courses,admin} + bachelors/ + further-education/<program>/ + research/
   - `software/` — projects/<repo>/{journal,decisions,ideas,README} + learning-paths/ + infrastructure/ + ideas/
   - `languages/` — german/english/spanish (each with classes/practice/consumed/grammars/idioms/flashcards/tracker)
   - `professional/` — career/{strategy,applications,interviews,certifications} + employers/ + clients/ + ventures/{restaurant-concept,online-shop,self-employed-services} + network/
   - `personal/` — sport/nutrition/cooking/music/travel/camper/finance/health (extensible: family/home/legal/reading/creative/…)

   ## 4. Naming conventions

   - Files in dated folders: `YYYY-MM-DD-<slug>.md` or `YYYY-MM-DD.md`
   - Per-event folders (talks, applications, trips): `YYYY-MM-DD-<slug>/`
   - Per-employer: `<year-range>-<slug>/` (e.g., `2024-2025-acme-corp/`)
   - Per-repo: `<repo-name>/` (must match GitHub repo slug)
   - MOCs: `MOC - <topic>.md` (always with " - " separator, lowercase topic)
   - Sources in raw/ post-triage: `YYYY-<slug>.<ext>` (year-only, slug)
   - All folder names: lowercase-kebab-case, English-only

   ## 5. Frontmatter contract

   Every note has YAML frontmatter. Universal fields:

   ```yaml
   ---
   type: <type>            # see per-domain types below
   title: ""
   date: YYYY-MM-DD
   language: en            # ar | de | en | es
   domain: <layer-domain>  # journal | stories | academic | software | languages | professional | personal | wiki-* | raw-*
   status: <status>        # draft | active | published | completed | archived (varies by type)
   tags: []
   ---
   ```

   Domain-specific fields (see templates in `_meta/templates/`):
   - journal: `seeds: []`, `references: []`
   - stories: `seed_from: ""`, `target_channel:`, `published_urls: {}`, `techniques_applied: []`
   - academic: `program:`, `grade:`, `linked_software:`, `supervisor:`
   - software: `project:`, `linked_academic:`, `linked_professional:`, `linked_wiki: []`
   - languages: `level:` (CEFR), `program:`
   - professional: `sub_domain:`, `linked_software: []`, `linked_venture:`, `linked_people: []`
   - personal: `sub_domain:`, `linked_journal:`, `linked_health:`, `mode:` (for travel)
   - wiki/concepts: `domain:`, `sub_domain:`, `sources: []`, `related_concepts: []`, `related_entities: []`, `mocs: []`
   - wiki/sources: `source_url:`, `source_author:`, `source_date:`, `extracted_concepts: []`, `extracted_entities: []`
   - raw: `captured: <iso8601>`, `source_url:`, `triaged_to: ""`

   ## 6. Cross-link rules

   - Software for client → `software/projects/<repo>/` + `professional/clients/<c>/deliverables/` cross-linked
   - Pitch deck for venture → `professional/ventures/<v>/investor-pitch.md` + `stories/spoken/pitches/<date>-<v>/` cross-linked
   - Cert credential PDF → `professional/career/certifications/<cert>/` (study material in `software/learning-paths/<cert>/`)
   - Academic project that's also code → both `academic/masters/courses/<x>/` and `software/projects/<repo>/`, cross-linked via `linked_*:` frontmatter
   - Trip in camper → `personal/travel/trips/<t>/` (canonical) with `mode: camper` + cross-link to `personal/camper/routes/`
   - Sport injury → `personal/sport/logs/<date>/` + `personal/health/conditions/<injury>/` cross-linked
   - Person mentioned in any note → wikilink to `wiki/entities/people/<name>.md` (canonical Person MOC)

   Rule: **one canonical home per artifact, cross-linked from peers via `linked_*:` frontmatter or `[[wikilinks]]`.** Never duplicate.

   ## 7. MOC convention (LYT, per Nick Milo)

   - Filename: `MOC - <topic>.md`
   - Frontmatter: `tags: [moc]`
   - Body: H2 sub-headings + `[[wikilinks]]` only — no inline content
   - Backlinks auto-tracked when any page contains `[[MOC - <topic>]]`
   - Master MOC: `wiki/mocs/MOC - home.md` is the vault entry point — every other MOC linked from it

   ## 8. LLM operations (Cascade workflow)

   **Ingest** (new source enters):
   1. Read source from `raw/_inbox/` or `raw/<media-type>/`
   2. Discuss key takeaways with Reebal (one source at a time)
   3. Write `wiki/sources/<type>/<slug>.md` (one-page card)
   4. Update or create `wiki/concepts/<domain>/<sub>/<concept>.md` for derived concepts
   5. Update or create `wiki/entities/<type>/<name>.md` for mentioned entities
   6. Add `[[MOC - <topic>]]` backlinks; update `wiki/mocs/` if a new topic emerges
   7. Update `wiki/index.md` (auto-maintained content index)
   8. Append `_meta/log.md`: `## [YYYY-MM-DD] ingest | <slug>`
   9. Move source from `_inbox/` to `raw/<media-type>/<year>-<slug>.<ext>` if not already

   **Query** (Reebal asks):
   1. Read `wiki/index.md` first to find relevant pages
   2. Drill into pages, follow links
   3. Synthesize answer with citations to wiki page IDs
   4. If answer is durable knowledge, file as `wiki/synthesis/<slug>.md` and update index
   5. Append `_meta/log.md`: `## [YYYY-MM-DD] query | <topic>`

   **Lint** (periodic health check):
   - Contradictions across pages
   - Stale claims superseded by newer sources
   - Orphan pages (no inbound links)
   - Concepts mentioned without their own page
   - Suggest new questions to investigate
   - Append `_meta/log.md`: `## [YYYY-MM-DD] lint | <findings-summary>`

   ## 9. Quality gate (Karpathy's load-bearing rule)

   > "A quality gate before storage matters more than anything else. Not everything belongs in the wiki."

   Decline to ingest if:
   - Low signal (noise, click-bait, duplicate-of-known)
   - Untrustworthy source without verification
   - Out of scope for any current MOC or domain
   - Already covered by an existing source — link to existing instead

   ## 10. Multilingual handling

   - Folder names: ALWAYS English (lowercase-kebab-case)
   - Note content language: frontmatter `language: ar | de | en | es`
   - Wiki/ pages default to English regardless of source language (translate if needed)
   - Originals/ pages match source/intent language
   - Arabic notes auto-render RTL via Obsidian's per-note language detection

   ## 11. Guardrails (what Cascade NEVER does)

   - Never modify files in `raw/<books|papers|clippings|voice|courses>/` post-triage (immutable)
   - Never modify files in `originals/` without Reebal's explicit instruction
   - Never delete a file — supersede with link (per cascade-system ADR-011)
   - Never create a wiki page without at least one inbound or outbound `[[wikilink]]`
   - Never bulk-create files without explicit confirmation
   - Never bypass the quality gate (Section 9)
   - Never store secrets/credentials in any vault file

   ## 12. Session start protocol

   Read in order at session start:
   1. `_meta/AGENTS.md` (this file)
   2. `_meta/log.md` (last 5-10 entries — recent activity context)
   3. `wiki/mocs/MOC - home.md` (master MOC — vault overview)
   4. Any context-specific MOC the conversation will touch (e.g., `MOC - data-science.md` if Reebal is asking about ML)

   ## 13. Co-evolution

   This schema co-evolves with use. When patterns emerge that this file doesn't capture,
   propose an update — don't silently improvise. Updates go through `@propose-extension`
   discipline: explicit ADR in `_meta/decisions/` if material, otherwise direct PR.
   ````

   - `_meta/log.md` — empty file with header `# Vault operations log\n\nAppend-only chronological record of LLM-driven operations on this vault.\n\n---\n`
   - `_meta/README.md` — single paragraph: "This is Reebal's personal knowledge vault, structured per ADR-023. Read `_meta/AGENTS.md` first to understand routing rules. Read `wiki/mocs/MOC - home.md` for content overview."
   - `_meta/templates/` — 8 template files: `atomic-note.md`, `journal-event.md`, `journal-daily.md`, `story-draft.md`, `concept-page.md`, `source-card.md`, `moc.md`, `application.md` (each with appropriate YAML frontmatter stub per AGENTS.md §5)
   - `_meta/scripts/` — empty directory; placeholder `.gitkeep`
4. Create `raw/` sub-folders: `_inbox/`, `books/`, `papers/`, `clippings/`, `voice/`, `courses/`
5. Create `originals/` 7 domain folders + their sub-shape per parent plan §10.3:
   - `originals/journal/{daily,events,weekly,monthly,yearly}/`
   - `originals/stories/written/{linkedin,blog,reflections}/{drafts,published}/`
   - `originals/stories/spoken/{presentations,pitches,meetings,conversations}/`
   - `originals/academic/{masters,bachelors,further-education,research}/` plus per-degree shape
   - `originals/software/{projects,learning-paths,infrastructure,ideas}/`
   - `originals/languages/{german,english,spanish}/{classes,practice,consumed,grammars,idioms,flashcards}/`
   - `originals/professional/{career,employers,clients,ventures,network}/` plus internal shape
   - `originals/personal/{sport,nutrition,cooking,music,travel,camper,finance,health}/` plus internal shape per §10.3
6. Create `wiki/` sub-structure per parent plan §10.5:
   - `wiki/index.md` — empty stub: `# Vault index\n\n*Auto-maintained by Cascade. Do not hand-edit.*\n`
   - `wiki/entities/{people,organizations,tools,places}/`
   - `wiki/concepts/{professional,software,academic,languages,personal,storytelling,crosscut}/` plus per-domain sub-folders per §10.5
   - `wiki/sources/{books,papers,articles,podcasts,videos,courses}/`
   - `wiki/synthesis/`
   - `wiki/mocs/MOC - home.md` — master MOC stub: `---\ntags: [moc]\n---\n\n# MOC - Home\n\n*Vault entry point. All other MOCs link from here.*\n\n## Domains\n\n*(MOCs added as content emerges)*\n`
7. Verify by opening Obsidian to vault and confirming v3 structure renders alongside legacy folders

**Deliverable:** v3 skeleton in place; nothing migrated yet; legacy folders untouched.

## Phase 2 — In-place migration (target: ~3-5 hours, can be split across sessions)

**Goal:** Move every existing file from legacy top-levels into its v3 home. Update wikilinks. Empty legacy folders.

**User directive (2026-05-04):** ALL existing content migrates (not just ~30-50 keepers). One vault only. Vault name stays `second-brain/`. Backup exists externally.

Per-top-level mapping is in parent plan §10.7. Execution approach:

1. Process top-levels in this order (smallest/clearest first):
   1. `Meta/` → likely just notes about vault itself; merge into `_meta/`
   2. `Personal/Camper/` → `originals/personal/camper/`
   3. `Personal/Reisen/` → `originals/personal/travel/trips/<per-trip-folder>/`
   4. `Personal/Ernährung/` → `originals/personal/nutrition/`
   5. `Personal/Sport/` → `originals/personal/sport/`
   6. `Personal/Budgett.numbers` → `originals/personal/finance/budget/` (extract to markdown if useful)
   7. `blogs/linkedin-posts/`, `blogs/web-site-blogs/` → `originals/stories/written/{linkedin,blog}/{drafts,published}/`
   8. `University/<each subfolder>/` → `originals/academic/masters/<thesis|courses|admin>/`
   9. `Projects/<each repo>/` → `originals/software/projects/<repo>/` (sparse — actual code stays in external repos; vault holds notes only)
   10. `Knowledge Hub/Storytelling Mastery/Untitled.md` → split 11 techniques into `wiki/concepts/storytelling/techniques/<technique-slug>.md` (one technique per file)
   11. `Knowledge Hub/<other topics>/` → triage individually to `wiki/concepts/<domain>/<sub>/` per content domain
   12. `✅ Kanban/` → review with user; likely archive (delete from vault, retain in backup) since Kanban boards don't fit markdown-vault paradigm
   13. `Archives/` → triage individually; relevant items to appropriate v3 homes; outdated items soft-deleted (removed from active vault, backup retains)
   14. `Inbox/` → bulk-move to `raw/_inbox/` (everything that hasn't been processed); user processes individually later

2. For each migration:
   - Move file to v3 location with appropriate filename (apply naming convention from AGENTS.md §4)
   - Add YAML frontmatter per AGENTS.md §5 (domain-specific fields)
   - Update any wikilinks in the moved file to point to new locations
   - Update any wikilinks in OTHER files that point to the moved file (use Obsidian CLI: `obsidian search query="[[<old-path>]]"` to find references)
   - Append `_meta/log.md`: `## [YYYY-MM-DD HH:MM] migrate | <old-path> → <new-path>`

3. After each top-level is fully empty, delete the legacy top-level folder

4. Track progress in `_meta/log.md`. Resume from log on session restart.

**Critical guardrail:** Don't bulk-move without per-file routing decisions for ambiguous items. When unclear, leave in `raw/_inbox/` for user triage.

**Deliverable:** vault root contains only `_meta/`, `raw/`, `originals/`, `wiki/`. All legacy content migrated or in `raw/_inbox/`.

## Phase 3 — Cleanup + validation (target: ~30 min)

1. Verify vault root contains exactly 4 folders: `_meta/`, `raw/`, `originals/`, `wiki/`
2. Run Obsidian's "Dataview" or built-in graph view to spot-check connectivity
3. Find orphan pages (no inbound or outbound links): `obsidian` CLI query for orphans
4. Test one round of Cascade operations:
   - Read `_meta/AGENTS.md` (verify it's the schema, not a stub)
   - Find a recent journal entry via `MOC - home.md` traversal
   - Create a test note in `originals/journal/daily/<year>/` per AGENTS.md routing
   - Verify the test note's frontmatter validates against AGENTS.md §5
5. Append `_meta/log.md`: `## [YYYY-MM-DD] migration-complete | summary: <N files migrated, M legacy folders deleted>`
6. Delete the test note created in step 4

**Deliverable:** validated v3 vault, ready for first real use.

## Phase 4 — First real use (ongoing)

From this point:
- All new captures land in `raw/_inbox/`
- All new authored content routes per AGENTS.md §2
- Cascade ingests sources into `wiki/` per AGENTS.md §8
- `_meta/log.md` accumulates ingest/query/lint events
- AGENTS.md co-evolves via `@propose-extension` discipline (cascade-system L1)

No specific deliverable — this is operational mode.

## ADR-023 authoring

Author AFTER Phase 3 validation, BEFORE Phase 4 first-real-use. Sequence:

1. Reserve `ADR-023` row in `cascade-system/docs/decisions/INDEX.md` (per ADR-009 reserve-first protocol). Title: "Vault layout convention (v3) + iCloud-symlink ratification". Status: Draft.
2. Author `cascade-system/docs/decisions/ADR-023-vault-layout-v3.md` with these sections:
   - **Status:** Accepted
   - **Context:** Issue #42 acceptance criteria; pre-restructure vault was chaotic (Inbox/Knowledge Hub/Personal/Projects/Archives/University/Meta/Kanban/blogs); user directive 2026-05-04 to greenfield restructure with extensibility-first principle
   - **Decision:** Adopt v3 spine (`_meta/` + `raw/` + `originals/` + `wiki/`); originals/ has 7 domains (journal, stories, academic, software, languages, professional, personal) per parent plan §10.3; wiki/ uses Karpathy type-first ontology (entities/concepts/sources/synthesis/mocs) per §10.5; raw/ per §10.4; `_meta/AGENTS.md` is the load-bearing schema per §10.6
   - **Migration:** in-place restructure per §10.7 (no rename, all content migrates)
   - **iCloud-symlink ratification:** canonical access path is `~/Projects/obsidian/second-brain` symlinked to `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain/` per ADR-022 + Obsidian CLI v1.12.7
   - **Consequences:** Reebal authors originals/, Cascade authors wiki/. Strict separation enforced. Co-evolution of AGENTS.md via @propose-extension. Future extensibility = new folder, no schema change.
   - **Sources consulted:** Karpathy gist (chunks 5-9, 14-22), llmwiki-cli reference, Nick Milo LYT MOCs, Mattgiaro 4,819-note vault, PARA, Zettelkasten, Johnny Decimal, ACCESS surveys
   - **Supersedes:** parent plan §3-5 (M2C.2 sequence revised), parent v2 plan §4 M2C.2 ("copy canonical vault to ~/Projects/obsidian-vault/" — superseded by in-place restructure)
3. User-approval checkpoint on ADR-023 draft. NON-SKIPPABLE.
4. After approval, commit ADR-023 + INDEX.md update via `/commit` workflow (use `@release-manager` for full PR lifecycle — see below)

## `@release-manager` flow

After ADR-023 user-approves:

1. `/branch-start docs/m2c2-vault-layout-v3` (or `/branch-start --worktree` if working alongside other Cascades)
2. Stage changes: ADR-023, INDEX.md update, any cascade-system documentation pointing to vault layout (e.g., `docs/cheat-sheet.md`, `AGENTS.md`)
3. `/commit` with message subject `docs: ADR-023 vault layout v3 (M2C.2)` and body referencing issue #42
4. `/branch-push-and-pr` — opens PR with `@release-manager`-conformant body
5. `/ci-watch` — likely no CI; reports clean exit ("no CI configured; merge gate is manual review only")
6. User reviews PR
7. `/branch-merge-and-cleanup` after approval
8. Close issue #42 with comment linking to ADR-023 + parent plan §10

## Scope guardrails (do NOT do these)

- **Do NOT re-litigate design.** Q5-Q23 are LOCKED. If something feels off during execution, capture to `cascade-system/queue/pending-review.md` for next `@sprint-review`. Don't pause execution to argue with the design.
- **Do NOT skip the user-approval checkpoint on ADR-023.** Issue #42 acceptance criteria require it.
- **Do NOT bulk-delete files without confirmation.** Soft-delete is supersede-with-link per ADR-011; relies on user's external backup for true deletion.
- **Do NOT touch M2C.3-M2C.6.** Those have separate issues and stay deferred until M2C.2 closes.
- **Do NOT modify `~/.windsurf/plans/cascade-c-obsidian-kickoff-3bf063.md`** (the parent plan) — it's archive-quality historical record. New work goes in this handoff or new ADRs.

## Refs

- Parent plan §10 (full design lock): `~/.windsurf/plans/cascade-c-obsidian-kickoff-3bf063.md` (lines 700+)
- Original handoff: `cascade-system/docs/handoffs/cascade-c-obsidian.md` (Sprint 1; partially superseded by ADR-022 + this handoff)
- Issue #42: https://github.com/ReebalSami/cascade-system/issues/42
- ADR-022 (Obsidian CLI selection, M2C.1): `cascade-system/docs/decisions/ADR-022-obsidian-cli-over-mcp.md`
- Karpathy LLM Wiki gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- llmwiki-cli reference implementation: https://github.com/doum1004/llmwiki-cli
- Nick Milo LYT MOCs: https://www.natecue.com/en/learn/productivity/map-of-content/
- Mattgiaro 4,819-note vault: https://mattgiaro.com/second-brain-obsidian/

## Promotion checklist

When fresh session starts execution, promote this file:

- [ ] Move `~/.windsurf/plans/m2c2-vault-execution-handoff.md` → `cascade-system/docs/handoffs/cascade-c-m2c2-vault-execution.md`
- [ ] Add row to `cascade-system/docs/handoffs/INDEX.md` for the new handoff
- [ ] Optionally update existing `cascade-system/docs/handoffs/cascade-c-obsidian.md` Status to "active — M2C.1 superseded by ADR-022, M2C.2 superseded by ADR-023" (or fold into M2C.6 retro PR)
