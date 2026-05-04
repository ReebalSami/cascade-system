# Cascade C2 Vault Write Path — Product Requirements Document

**Phase**: spec
**Phase set**: cascade-system meta-repo verticals (no `phases.yaml`; M_C2.x milestones = phase sequence per ADR-003)
**Status**: Approved
**Date**: 2026-05-04
**Source brainstorm**: `docs/prompts/stages/m_c2-1-brainstorm.md`
**Milestone**: [M_C2.1 — Decision sprint](https://github.com/ReebalSami/cascade-system/issues/63)
**Successor phase**: M_C2.2 — ADR-030 ratification PR (via `@propose-extension` → `@release-manager`)

## 1 — Problem

Sprint 2 Vertical C built the vault **READ path** (5 accepted ADRs: ADR-024 `@vault-research`, ADR-025 `@grill-me` step 1.6 integration, ADR-026 `@to-prd` §13 sources, ADR-027 `/recalibrate` 7th drift signal, ADR-028 `obsidian-context-priming` rule). These primitives let Cascade *read* the vault — but the vault's `wiki/` layer is empty. Per `_meta/AGENTS.md` §1, `wiki/` is **Cascade-authored territory** (the layer where "LLM-distilled durable knowledge" lives) — yet no mechanism exists to write to it. The READ/WRITE asymmetry breaks the intended vault architecture established in ADR-023.

Karpathy's LLM-wiki gist + the 4 Karpathy-adjacent clippings at `~/Projects/obsidian/second-brain/raw/clippings/` (surfaced during M2C.2 vault migration) describe a **knowledge-base-compiler pattern**: sources get ingested, distilled into typed `wiki/` entries (concepts, entities, sources, syntheses), quality-gated per `_meta/AGENTS.md` §9, and cross-linked via `[[wikilinks]]` to enable LLM-augmented retrieval. The pattern is load-bearing for the vault's design — and currently has no implementation.

This vertical (Cascade C2, milestones M_C2.1–M_C2.5) closes the asymmetry by designing and shipping a vault WRITE path. M_C2.1 (this PRD) decides the architecture; M_C2.2 ratifies the ADR↔vault relationship in a dedicated ADR; M_C2.3 authors the chosen skill; M_C2.4 dogfoods it on the 4 Karpathy clippings, producing the vault's first `wiki/` content; M_C2.5 closes the sprint and unblocks Vertical B (python-ml-uv L3 template).

## 2 — Goals

- **G1 — READ/WRITE skill symmetry**: a user-invoked auxiliary L1 skill `@vault-distill` bookends `@vault-research` — same utility tier, same invocation shape, opposite direction. **Acceptance**: skill at `~/.codeium/windsurf/skills/vault-distill/SKILL.md` passes `@verify-l1` (ADR-029) sweep; both SKILL.md docstrings reference the symmetric pair.
- **G2 — Pre-write §9 quality gate**: every distillation run passes the AGENTS.md §9 quality gate **before** anything reaches disk. Decline-low-signal / untrustworthy / out-of-scope / duplicate is a pre-write decision, not post-hoc cleanup. **Acceptance**: every commit's `preview-summary.md` shows per-source §9 decisions (PASSED/DECLINED + reasons); declined sources never reach disk; ADR-011 supersession-debt avoided by design.
- **G3 — ADR↔vault Posture B ratified**: a dedicated ADR (ADR-030) declares the unified-discovery posture with a self-demonstrating dogfood card and a sustainable backfill policy. **Acceptance**: ADR-030 merged; dogfood card at `wiki/sources/adrs/cascade-system/cascade-system_adr-030-adr-vault-posture.md` exists; lazy-backfill policy + debt-trigger escalation documented.
- **G4 — Project-scoped namespacing day-1**: project scope is built into the ADR-card shape from the start — both structurally (subdirectory) and lexically (filename prefix) — so cascade-system, Vertical B, Vertical D, and future projects' ADRs stay unambiguously separated. **Acceptance**: ADR-030 frontmatter spec includes `project:` field; cards path-prefixed AND filename-prefixed by project slug (`_` separator); wikilink basename self-identifies project.
- **G5 — Vault's first `wiki/` content via M_C2.4 dogfood**: distilling 4 Karpathy clippings produces ~12–15 wiki files (sharing concepts/entities across sources), all passing §9, all reviewed in Obsidian dry-run preview before commit. **Acceptance**: ≥10 wiki files committed (split per brainstorm lines 345–353); 1 merged Karpathy entity card (not 4 duplicates); all `[[wikilinks]]` either resolve or are flagged for lazy-backfill; ADR-023 §Evidence off-by-one corrected (3→4).
- **G6 — Pure-filesystem vault writes**: vault is not git-versioned per ADR-023 §Consequences. No git mutations against the vault. Repo-side artifacts (ADR-030 body, SKILL.md, PRD, retro) land via `@release-manager` per ADR-018. **Acceptance**: no git commands target the vault; `@release-manager` only runs against repo-side artifacts; PRs that touch vault state acknowledge outside-repo-edit in body.
- **G7 — v2 evolution path named, not architectured**: the conversation-trigger pattern (Option 3) ships as a v2 candidate as a **second activation mode on the same skill**, not as a new skill. v1 SKILL.md procedure designed extensibly so a `--from-findings=<file>` flag is additive. **Acceptance**: §10 O17 names the v2 path; v1 SKILL.md doesn't preclude the v2 activation site.

## 3 — Non-goals

Lifted from brainstorm §Non-goals (lines 43–50):

- **N1** — Scheduled flush / cron / session-hook distillation. Blocked by GA-3 (Windsurf has no `SessionStart` / `PreCompact` / `SessionEnd` hooks). Revisit when platform ships them.
- **N2** — Fully-automatic conversation-triggered distillation. Named as v2 candidate (§10 O17); not committed in v1.
- **N3** — Distillation of `originals/` content. Skill refuses at boundary with explicit error pointing to queue → ADR escape hatch. Future ADR when a real use case emerges.
- **N4** — Generalized project-namespacing across all `wiki/sources/<type>/` subtypes (papers, READMEs, meetings, courses, voice memos). v1 scopes project-namespacing to ADR cards only.
- **N5** — `@vault-distill --undo` mode. ADR-011 (supersession over deletion) forbids plain delete. v2 design space (§10 O18).
- **N6** — Immediate full backfill of 29 existing cascade-system ADRs. Forward-only + lazy-backfill is the policy; debt-trigger escalation if backlog crosses ~10 ADRs.
- **N7** — Git versioning of the vault. Unchanged per ADR-023 §Consequences.
- **N8** — Multi-machine vault sync beyond iCloud. ADR-022 + ADR-023 establish iCloud as the sync mechanism; this vertical doesn't change that.

## 4 — Users

| User | Description | Priority |
|---|---|---|
| Reebal | Primary skill-invoker; vault owner; reviews preview dirs in Obsidian before commit; owns `originals/`-scope decisions | primary |
| Cascade (tool persona) | Skill-invoker for ADR-030 dogfood (M_C2.2) and dogfood pass on Karpathy clippings (M_C2.4); future end-of-skill caller for v2 conversation-trigger mode (§10 O17) | secondary |
| Future-project Cascades | Vertical B (python-ml-uv L3), Vertical D (thesis), and future projects each ship project-namespaced ADRs that vault-card via the same skill — project namespacing pays off across the full cascade-system constellation | tertiary |

## 5 — Use cases

Concrete scenarios the skill must support. Each maps to one or more vertical slices in §11.

1. **Single-clipping distill** — As Reebal, I invoke `@vault-distill raw/clippings/karpathy-wiki.md` and review a tempdir preview of the proposed source card + extracted concepts/entities + MOC update + log append before approving the commit. (Maps to Slice 4.)
2. **Multi-source merge** — As Reebal, I invoke `@vault-distill raw/clippings/` over the 4 Karpathy clippings and the skill produces ONE merged `wiki/entities/people/karpathy.md` card with all 4 source backrefs (not 4 duplicate cards). Shared concepts get one card each with multi-source provenance. (Maps to Slice 4.)
3. **ADR card creation (dogfood)** — As Cascade landing ADR-030, I produce `wiki/sources/adrs/cascade-system/cascade-system_adr-030-adr-vault-posture.md` per Posture B + Shape C, demonstrating the namespacing pattern self-referentially. (Maps to Slice 2.)
4. **§9 quality gate decline** — As Reebal, the skill flags 1 of N sources as low-signal in `preview-summary.md` with reasoning; I edit the preview by deleting the flagged card; commit drops it from the write set; declined source never touches disk per G2. (Maps to Slice 4.)
5. **Edit-before-commit miscategorization fix** — As Reebal, the skill miscategorizes a concept under `wiki/concepts/software/` when it should live under `wiki/concepts/professional/`; I edit the preview file's path/frontmatter directly in Obsidian; `--commit` carries the edit through to the final write atomically. (Maps to Slice 4.)

## 6 — Success criteria

Per-milestone close-conditions. Each is observable post-shipping.

**M_C2.1 — Decision sprint**

- [x] `docs/prompts/stages/m_c2-1-brainstorm.md` shipped with `Status: Approved`
- [ ] `docs/prompts/stages/m_c2-1-prd.md` shipped with `Status: Approved` (this artifact)
- [ ] PR landing both M_C2.1 artifacts merged via `@release-manager`
- [ ] GitHub issue [#63](https://github.com/ReebalSami/cascade-system/issues/63) closed with link to PRD

**M_C2.2 — ADR-030 ratification PR**

- [ ] ADR-030 row reserved in `docs/decisions/INDEX.md` per ADR-009 (reserve-first)
- [ ] `docs/decisions/ADR-030-adr-vault-posture.md` authored and merged
- [ ] Vault filesystem mutation (no git per C2): dogfood card at `wiki/sources/adrs/cascade-system/cascade-system_adr-030-adr-vault-posture.md`
- [ ] Vault filesystem mutation (no git per C2): `_meta/AGENTS.md` updated — §2 routing row for `_meta/.preview/` + §5 `source_type: adr` frontmatter spec
- [ ] `@verify-l1` green
- [ ] M_C2.2 GitHub issue closed

**M_C2.3 — `@vault-distill` skill authoring**

- [ ] `~/.codeium/windsurf/skills/vault-distill/SKILL.md` authored at L1 canonical path (per ADR-014) via `@propose-extension` → `@write-skill` flow (per ADR-017)
- [ ] SKILL.md frontmatter passes ADR-006 schema + ADR-029 verify-l1 sweep
- [ ] Sources consulted (per `adapt-from-all`) cite: 4 Karpathy clippings + `@vault-research` SKILL.md + relevant entries from `refs/superpowers/`, `refs/claude-skills/`, `refs/awesome-agent-skills/`, `refs/mattpocock-skills/`
- [ ] PR includes outside-repo-edit acknowledgment (skill lives under `~/.codeium/windsurf/`)
- [ ] `@vault-research` ↔ `@vault-distill` symmetric-pair reference captured in both SKILL.md docstrings post-merge
- [ ] M_C2.3 GitHub issue closed

**M_C2.4 — Karpathy clippings dogfood + ADR-023 evidence correction**

- [ ] `@vault-distill raw/clippings/` invocation produces `_meta/.preview/<timestamp>_karpathy-clippings-distill/` preview dir
- [ ] User reviews mirrors in Obsidian; approves or edit-before-commit
- [ ] On approval: ≥10 wiki files committed (split: 4 source cards, 1 merged Karpathy entity, 5–8 shared concepts, 1 MOC, `wiki/index.md` update, `_meta/log.md` 4-entry append; 0 raw moves)
- [ ] All committed cards pass §9 gate (or were declined via edit-before-commit per G2)
- [ ] All `[[wikilinks]]` either resolve or are flagged in `preview-summary.md` as lazy-backfill candidates per Q2b
- [ ] `docs/decisions/ADR-023-vault-layout-v3.md` §Evidence corrected: *"3 clippings moved" → "4 clippings moved"* (off-by-one identified in brainstorm C9)
- [ ] M_C2.4 GitHub issue closed

**M_C2.5 — Sprint retro + Vertical B unblock**

- [ ] Sprint retro authored at canonical path per ADR-003 (`retros/sprint-2-vertical-c2.md` or equivalent) with sections: what shipped (M_C2.1–M_C2.4), brainstorm-design-validation (which decisions held vs. shifted), friction observations, L1 promotion candidates queued in `cascade-system/queue/pending-review.md`, v2 backlog (O17–O20) explicitly named
- [ ] Retro `Status: closed`
- [ ] Vertical B handoff (`docs/handoffs/cascade-b-template-python-ml-uv.md`) blocker note removed (B-unblock per C4)
- [ ] All M_C2.x GitHub issues closed; M_C2.x milestone closed
- [ ] Clean working tree at retro close (per ADR-008)

## 7 — Solution overview

`@vault-distill` is a user-invoked auxiliary L1 skill that bookends `@vault-research` (READ → WRITE symmetry). It compiles a `raw/`-resident source (single file, directory, or multi-path) into vault `wiki/` content per `_meta/AGENTS.md` §8 ingest workflow, applying the §9 quality gate pre-write, surfacing all proposed mutations in a tempdir preview the user reviews in Obsidian before atomic commit.

**Architecture (5 concerns):**

1. **Input handling + scope enforcement.** Skill accepts path / directory / multi-path arguments (glob via shell expansion). Detects flow by path prefix: `raw/_inbox/**` → triage-and-ingest (includes `_inbox/ → <type>/` move per AGENTS.md §8 step 9); `raw/{books,papers,clippings,voice,courses}/**` → post-triage-distill (no move). Refuses `originals/**`, `_meta/**`, `wiki/**`, paths outside vault root with explicit error pointing to queue → ADR escape hatch.

2. **Distillation engine** (LLM-driven). Reads source via `obsidian read` (never raw filesystem — C8 iCloud-stub mitigation). Per source: extracts source-card metadata (1-page summary, key claims), concepts (per AGENTS.md §2 → `wiki/concepts/<domain>/<sub>/`), entities (people / organizations / tools / places per AGENTS.md §2). Multi-source runs perform extract-then-merge: dedupe entities/concepts by canonical-name across all sources, emit ONE card per canonical entity with all source backrefs (resolves §10 O6). Reconciliation against existing vocabulary via `obsidian search query=<candidate-name>` before card-creation.

3. **§9 quality gate.** Per-source decline check (low-signal / untrustworthy / out-of-scope / duplicate per AGENTS.md §9). Gate is **flagging not deciding**: skill marks decline candidates with reasons in `preview-summary.md`; user actions the decline by deleting the flagged file from preview dir before commit (resolves §10 O13). Duplicate detection v1 = SHA-256 on normalized body (semantic similarity is v2; resolves §10 O12).

4. **Tempdir preview infrastructure.** Default mode = dry-run; explicit `--commit` triggers atomic write. Preview lives at `_meta/.preview/<ISO8601-timestamp>_<input-basename-or-dir>/` with structure:

    ```
    _meta/.preview/2026-05-04T143022_karpathy-clippings-distill/
    ├── preview-summary.md        ← MANDATORY entry point (diff-style overview + §9 decline flags + unresolved [[wikilinks]])
    ├── mirrors/                  ← Obsidian-openable file mirrors of every proposed write
    │   ├── wiki/sources/.../<card>.md
    │   ├── wiki/concepts/.../<concept>.md
    │   ├── wiki/entities/.../<entity>.md
    │   ├── wiki/mocs/MOC - <topic>.md
    │   ├── wiki/index.md         (updated version)
    │   └── _meta/log-append.md   (proposed §8 step 8 append text)
    └── raw-move.manifest         ← describes raw/_inbox/ → raw/<type>/ moves (empty for post-triage flow)
    ```

    User reviews mirrors in Obsidian (same renderer as post-commit). Edit-before-commit is a killer feature: user edits mirror file directly, edit carries through. Capture SHA-256 of all target file paths at preview time; commit re-checks; refuses with §10 O9 remediation message on divergence.

5. **Atomic commit + cleanup.** `@vault-distill --commit [<slug>]` (defaults to latest preview if no slug) performs all 7 atomic mutations (per brainstorm Q4 lines 213–220) as a unit. Success → delete preview dir. Explicit abandonment via `--discard <slug>`. Auto-GC previews >7 days old on next invocation. Cards' frontmatter includes `preview_slug:` field for audit-trail backreference (resolves §10 O16).

**ADR card shape** (Posture B per brainstorm Q2 + Shape C per Q2a):

- Path: `wiki/sources/adrs/<project-slug>/<project-slug>_adr-<NNN>-<kebab-slug>.md` (e.g., `wiki/sources/adrs/cascade-system/cascade-system_adr-030-adr-vault-posture.md`)
- Wikilink: `[[<project-slug>_adr-<NNN>-<kebab-slug>]]` (basename-self-identifying; survives paste-and-carry + file moves)
- Separator: `_` (matches existing vault practice — date-slug separator in `2026-05-04_<title>.md`)
- Frontmatter: per brainstorm Q2a spec lines 130–146 (`type: source`, `source_type: adr`, `project`, `adr_id` zero-padded string, `title`, `date`, `source_path` repo-relative back-pointer, `status`, `supersedes`, `superseded_by`, `linked_adrs`, `extracted_concepts`, `extracted_entities`, `tags`).

**Backfill policy** (per brainstorm Q2b): forward-only + lazy-backfill of existing 29 cascade-system ADRs. Unresolved-wikilink tolerance accepted (Obsidian renders gracefully). Debt-trigger escalates to `cascade-system/queue/pending-review.md` if un-carded-ADR-citation backlog crosses ~10 ADRs across open `wiki/` work (resolves §10 O21).

**`_meta/AGENTS.md` extensions** (ship with ADR-030 PR per M_C2.2): §2 routing-table row for `_meta/.preview/` (transient, dot-prefix ignorable, auto-GC); §5 new subtype `source_type: adr` with the frontmatter spec above.

**Skill location**: `~/.codeium/windsurf/skills/vault-distill/SKILL.md` (L1 canonical path per ADR-014). Authored via `@propose-extension` → `@write-skill` flow per ADR-017. `@verify-l1` green before merge (ADR-029). Sources consulted will include the 4 Karpathy clippings + `@vault-research` SKILL.md (READ bookend) + AGENTS.md §8/§9/§11 + relevant superpowers/claude-skills/awesome-agent-skills entries.

## 8 — Constraints

Lifted from brainstorm §Constraints (lines 54–62):

- **C1 — Cascade-system is a meta-repo vertical**. No `phases.yaml`; M_C2.x milestones are the phase sequence. Artifact paths follow ADR-003 strict-docs-structure: brainstorm + PRD at `docs/prompts/stages/`, ADR at `docs/decisions/`, retro at `retros/`.
- **C2 — Vault is not git-versioned**. All vault writes are pure filesystem mutations. No commit discipline at vault level. `@release-manager` discipline applies only to repo-side artifacts.
- **C3 — Bundle anti-pattern enforced** (per `@update-horizontal` SKILL.md + handoff §8). ADR-030 (M_C2.2) and `@vault-distill` SKILL.md (M_C2.3) ship in **separate** PRs. Bundling them violates one-ADR-per-logical-change.
- **C4 — B-unblock gate**. Vertical B (python-ml-uv L3 template) stays blocked until C2's retro (M_C2.5) closes. No edits to B's handoff in any C2 PR.
- **C5 — AGENTS.md §9 quality gate is load-bearing**. Every distilled card must pass: high signal, trustworthy, in-scope for some MOC/domain, non-duplicate. Gate fires pre-write.
- **C6 — AGENTS.md §11 guardrails apply**. Skill must never: modify `raw/<books|papers|clippings|voice|courses>/` post-triage; modify `originals/` without explicit instruction; delete a file (supersede with link per ADR-011); create a wiki page without at least one `[[wikilink]]` in/out; bulk-create without confirmation; store secrets in any vault file.
- **C7 — AGENTS.md §10 multilingual posture**. Folder names English-only lowercase-kebab-case. Wiki pages default to English regardless of source language (translate if needed). Source language in frontmatter `language:`.
- **C8 — iCloud materialization risk**. ADR-022 documented the iCloud 0-byte-stub failure mode. The `obsidian` CLI interacts with Obsidian's in-memory index, so this class of failure doesn't surface there — but filesystem-direct operations (`ls`, `find`) can still return ghost-empty results. Skill must use `obsidian files` / `obsidian read` primitives for source reads, not direct filesystem globs, to stay on the reliable path.
- **C9 — 4 Karpathy clippings are the M_C2.4 dogfood corpus** (verified present at `raw/clippings/` during brainstorm orientation): `2026-05-04_i-built-self-evolving-claude-code-memory-w-karpathys-llm-knowledge-bases.md` (canonical, 23 KB), `2026-05-04_karpathys-wiki-vs-open-brain-one-fails-when-you-need-it-most.md` (largest, 47 KB), `2026-05-04_karpathys-obsidian-rag-plus-claude-code-equal-cheat-code.md` (16 KB), `2026-05-04_i-used-karpathy's-autoresearch-to-train-an-llm.md` (16 KB). ADR-023 §Evidence currently says "3 clippings moved" — off-by-one; correction note planned in M_C2.4 PR.

## 9 — Risks & mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Preview leakage to git via `_meta/.preview/` accidental commit | low | low | Vault is not git-versioned (C2); `_meta/.preview/` documented in AGENTS.md §2 routing as transient/dot-prefix-ignorable |
| SHA-256 divergence false-positive on legitimate user edit between preview + commit | med | low | §10 O9 remediation message; `--merge <slug>` re-generates preview against current state (v2 if friction warrants) |
| Malformed / unreadable source crashes skill mid-batch | low | med | §10 O8 skip-with-warning in `preview-summary.md`; other sources continue |
| §9 gate over-decline (skill rejects legitimate content) | med | med | Decline is **flagged + reasoned**, not auto-applied; user decides via edit-before-commit (§10 O13) |
| iCloud 0-byte stub on filesystem-direct read | low | high | C8: skill uses `obsidian read` / `obsidian files` only, never raw `ls` / `find` globs |
| Supersession-debt accumulation from miscommitted cards (ADR-011 forbids delete) | med | med | Dry-run default (Mode B) + edit-before-commit kill-feature; trust tier earnable post-10-runs (§10 O17 v2 ADR) |
| Concurrent invocation produces preview-dir collision | low | low | §10 O10 refuse-with-active-preview; user runs `--commit` or `--discard` first |
| Unresolved-wikilink backlog grows past lazy-backfill capacity | med | med | Brainstorm Q2b debt-trigger escalation to `cascade-system/queue/pending-review.md` at ~10 un-carded ADRs |

## 10 — Open questions

All 21 questions from brainstorm §Open questions get best-guess + owner + re-evaluate trigger. Best-guesses unblock delivery; re-evaluation triggers prevent silent ossification.

### Skill-signature questions (O1–O3)

| # | Question | Best guess | Owner | Re-evaluate when |
|---|---|---|---|---|
| O1 | Exact v1 flag set | `--commit [<slug>]`, `--discard <slug>`, `--preview=tempdir\|inline`, `--interactive`, `--clean-previews`. Drop `--dry-run` (redundant with default), drop `--verbose` (out-of-scope for v1) | Cascade | After M_C2.4 dogfood reveals missing flags |
| O2 | Invocation argument shapes | Path + directory + multi-path supported in v1; glob via shell expansion (skill receives expanded list); topic-based deferred to v2 (O3) | Cascade | After M_C2.4 dogfood |
| O3 | Topic-based invocation `topic=<x>` | v2 — defer until `@vault-research` integration matures and topic→`raw/` resolution is stable | User | When `@vault-research` has stable topic→source mapping |

### Concept/entity extraction mechanics (O4–O7)

| # | Question | Best guess | Owner | Re-evaluate when |
|---|---|---|---|---|
| O4 | Concept-extraction prompt shape + dedup against existing `wiki/concepts/` | Prompt template embedded in SKILL.md procedure; LLM extracts via in-context reasoning (no separate model). Dedup via `obsidian search query=<concept-candidate>` before card-create; suggest-merge prompt in `preview-summary.md` if near-match found | Cascade | After M_C2.4 dogfood reveals miscategorization patterns |
| O5 | Entity types + canonical-name resolution | AGENTS.md §2's exact 4 (people/organizations/tools/places); canonical-name resolution via `obsidian search` over `wiki/entities/<type>/` first; on conflict (e.g., "Andrej Karpathy" vs "Karpathy"), flag in preview-summary for user merge | Cascade | After M_C2.4 dogfood |
| O6 | Shared-concept/entity merging across multi-source runs | Single-pass: extract all → normalize → dedupe by canonical-name → emit one card per canonical with all source backrefs. Algorithm specified in SKILL.md procedure | Cascade | After M_C2.4 dogfood (4 clippings sharing "Karpathy" entity is the load-bearing test) |
| O7 | MOC update semantics (new vs add-to-existing) | Heuristic: ≥3 concepts share a topic without existing MOC → propose new MOC in preview; ≤2 → link to nearest existing MOC. User overrides via edit-before-commit | Cascade | After M_C2.4 dogfood |

### Error handling + edge cases (O8–O11)

| # | Question | Best guess | Owner | Re-evaluate when |
|---|---|---|---|---|
| O8 | Malformed / unreadable / too-large source | Skip with warning in `preview-summary.md` (file path + reason); other sources continue. Size threshold v1 = none (LLM context window is the natural cap; emit warning if `obsidian read` returns truncated content) | Cascade | After first malformed-source incident |
| O9 | SHA-256 divergence detection failure recovery | Skill emits concrete remediation: *"Targets mutated since preview. Run `@vault-distill --commit <slug> --merge` to regenerate preview against current state, or `--discard <slug>` and re-invoke fresh."* `--merge` flag is v2 if friction warrants | Cascade | After first divergence incident |
| O10 | Concurrent invocations | Refuse with message: *"Active preview at `<slug>` from `<timestamp>`. Run `--commit <slug>` or `--discard <slug>` first."* No serialize | Cascade | After first concurrent-invocation friction |
| O11 | `raw/_inbox/` move conflicts | Suffix timestamp on collision (e.g., `2026-05-04-karpathy-wiki_T143022.md`); flag in preview-summary for user-confirm before commit | Cascade | After first triage-and-ingest run (M_C2.4 expects 0 raw moves) |

### Quality-gate mechanics (O12–O14)

| # | Question | Best guess | Owner | Re-evaluate when |
|---|---|---|---|---|
| O12 | Duplicate-content detection | v1 = SHA-256 on normalized source body (whitespace + frontmatter stripped); semantic similarity is v2. Flag hash collision in `preview-summary.md`; user decides | Cascade | After M_C2.4 dogfood (no exact-dupes expected) |
| O13 | Low-signal / untrustworthy decision authority | Skill **flags** with reasons in `preview-summary.md` (per-source: `signal: high\|medium\|low\|declined-low-signal`, `trust: <reason>`); user decides via edit-before-commit (delete flagged file from preview dir to decline) | Cascade | After M_C2.4 dogfood reveals over/under-decline patterns |
| O14 | Out-of-scope detection | Skill checks against existing `wiki/mocs/MOC - <topic>.md` set + AGENTS.md §3 originals/ domain map; flags out-of-scope candidates with *"no matching MOC; suggest creating MOC - `<inferred-topic>` or declining"*; passes through for user decision | Cascade | After M_C2.4 dogfood |

### Log + audit trail (O15–O16)

| # | Question | Best guess | Owner | Re-evaluate when |
|---|---|---|---|---|
| O15 | `_meta/log.md` append format | Per AGENTS.md §8 exact: `## [YYYY-MM-DD] ingest \| <slug>` + body (3-5 lines: source count, card count, flags used, preview-slug for audit reference) | Cascade | After M_C2.4 if format friction emerges |
| O16 | Preview-to-commit audit trail | Committed cards' frontmatter includes `preview_slug: <ISO8601-timestamp>_<slug>` field; `_meta/log.md` append text matches; allows reproducibility-trace from commit back to preview decisions | Cascade | After M_C2.4 |

### v2 candidates (O17–O20)

| # | Question | Best guess | Owner | Re-evaluate when |
|---|---|---|---|---|
| O17 | Option 3 conversation-trigger trigger heuristic | Defer to v2 ADR. Likely shape: `@grill-me` step 9 / `@to-prd` step 9 transition explicitly calls `@vault-distill --from-findings=<session-findings-file> --preview=inline`; user opts in/out per-session via session-start flag | User | When `@vault-distill` has 10+ successful invocations + Windsurf ships SessionEnd hook |
| O18 | `--undo` shape (given ADR-011 forbids delete) | Defer to v2. Likely shape: supersession-with-link (target card frontmatter `superseded_by: <new-slug>` + new card with `supersedes: <old-slug>`) at undo time, prompted via `@vault-distill --undo <slug>` flag | User | When first wrong-card-on-disk incident occurs |
| O19 | Generalized project-namespacing for non-ADR source types | Defer until 2nd source type hits same pressure. Future ADR cites ADR-030 as precedent + extends namespacing pattern to that source type only (e.g., papers → `wiki/sources/papers/<project>/<project>_<paper-slug>.md`) | User | When 2nd source type reveals project-scope collision |
| O20 | `originals/` scope expansion | Defer. Future ADR with privacy-scoping mechanism (e.g., per-invocation `--scope=originals/software/projects/<repo>/` flag with explicit user consent prompt) | User | When first concrete originals/ distillation use case emerges (e.g., thesis-note distillation into `wiki/concepts/research/`) |

### Backfill debt-trigger (O21)

| # | Question | Best guess | Owner | Re-evaluate when |
|---|---|---|---|---|
| O21 | Debt-trigger queue-entry shape + drainer | Queue entry shape: `## [YYYY-MM-DD] vault-backfill-debt \| <project>` + body lists un-carded ADRs with their pending wiki citations + the `[[wikilink]]` paths waiting resolution. Drained by `@sync-github` at next milestone close OR by explicit `@vault-distill --backfill <project>/<from>-<to>` flag (v2 if needed) | Cascade | When first un-carded-ADR-citation count crosses ~10 |

## 11 — Vertical slices

Five slices, dependency-ordered, 1:1 mapping to milestones M_C2.1–M_C2.5. Each slice is independently shippable end-to-end with observable acceptance criteria.

### Slice 1: M_C2.1 — Decision sprint (this PRD)

- **Goal**: Lock the architecture for the vault WRITE path through approved brainstorm + approved PRD ratifying §7 solution overview + §10 open-question best-guesses + §11 slice decomposition.
- **Acceptance**:
  - `docs/prompts/stages/m_c2-1-brainstorm.md` shipped with `Status: Approved` ✅ (done)
  - `docs/prompts/stages/m_c2-1-prd.md` shipped with `Status: Approved` (this slice's deliverable)
  - GitHub issue #63 closed with link to PRD artifact
  - Both artifacts land in a single PR via `@release-manager`
- **Dependencies**: none
- **Suggested milestone**: M_C2.1 (issue [#63](https://github.com/ReebalSami/cascade-system/issues/63))
- **Suggested labels**: `vertical-slice`, `cascade-c2`, `prd`, `meta-repo`
- **Notes**: Brainstorm exhaustiveness (4 main approaches resolved + 21 open questions enumerated) collapses the typical PRD-drafting interview surface. PRD = mechanical translation. No `@to-issues` invocation needed — M_C2.x issues already exist per `@kickoff` orientation.

### Slice 2: M_C2.2 — ADR-030 ratification PR

- **Goal**: Ratify Posture B (ADR↔vault unified discovery via project-namespaced summary cards) as a load-bearing decision with self-demonstrating dogfood card + AGENTS.md schema extensions.
- **Acceptance**:
  - `docs/decisions/INDEX.md` row reserved for ADR-030 per ADR-009 (reserve-first)
  - `docs/decisions/ADR-030-adr-vault-posture.md` authored (Posture B + Shape C + forward-only lazy-backfill policy)
  - Vault filesystem mutation (no git per C2): `wiki/sources/adrs/cascade-system/cascade-system_adr-030-adr-vault-posture.md` (dogfood card)
  - Vault filesystem mutation (no git per C2): `_meta/AGENTS.md` updated — §2 `.preview/` routing row + §5 `source_type: adr` frontmatter spec
  - PR contains ADR-030 body + INDEX update only (per C3 bundle anti-pattern: ADR ratification ≠ skill authoring); vault edits land between PRs as filesystem-only ops
  - `@verify-l1` green
  - M_C2.2 GitHub issue closed (verify exists or file via `@sync-github`)
- **Dependencies**: Slice 1 (PRD ratifies the posture; ADR-030 can't precede PRD)
- **Suggested milestone**: M_C2.2
- **Suggested labels**: `vertical-slice`, `cascade-c2`, `adr`, `vault-schema`
- **Notes**: PR shape per brainstorm lines 320–331. Two vault mutations land outside the PR (vault is not git-versioned per C2). Acknowledge outside-repo-edit in PR body.

### Slice 3: M_C2.3 — `@vault-distill` skill authoring

- **Goal**: Ship the user-invoked WRITE skill at L1 canonical path, fully shaped per §7 solution overview.
- **Acceptance**:
  - `~/.codeium/windsurf/skills/vault-distill/SKILL.md` authored via `@propose-extension` → `@write-skill` flow (ADR-017)
  - SKILL.md frontmatter passes ADR-006 schema + ADR-029 verify-l1 sweep
  - Procedure section covers: input validation, flow detection, LLM extraction prompt template, §9 gate flagging, preview-dir generation, SHA-256 divergence detection, atomic commit, GC policy
  - Sources consulted (per `adapt-from-all`) cite: 4 Karpathy clippings + `@vault-research` SKILL.md + relevant entries from `refs/superpowers/`, `refs/claude-skills/`, `refs/awesome-agent-skills/`, `refs/mattpocock-skills/`
  - PR includes outside-repo-edit acknowledgment (skill lives under `~/.codeium/windsurf/`, not the cascade-system repo)
  - M_C2.3 GitHub issue closed
- **Dependencies**: Slice 2 (skill body references ADR-030 frontmatter spec for ADR-card subtype)
- **Suggested milestone**: M_C2.3
- **Suggested labels**: `vertical-slice`, `cascade-c2`, `skill`, `l1`
- **Notes**: Skill lives outside cascade-system repo; PR lands the queue entry + ADR change-log entry + any AGENTS.md-pointer updates only. Symmetric pair `@vault-research` ↔ `@vault-distill` captured in both SKILL.md docstrings post-merge.

### Slice 4: M_C2.4 — Karpathy clippings dogfood + ADR-023 evidence correction

- **Goal**: First real run of `@vault-distill` produces vault's first `wiki/` content from the 4 Karpathy clippings, validating the design end-to-end and surfacing real-world friction for the M_C2.5 retro.
- **Acceptance**:
  - Invocation: `@vault-distill raw/clippings/`
  - Preview dir produced at `_meta/.preview/<timestamp>_karpathy-clippings-distill/`; user reviews in Obsidian; user commits or discards
  - On commit: ≥10 wiki files land (rough split per brainstorm lines 345–353: 4 source cards, 1 merged Karpathy entity, 5–8 shared concepts, 1 MOC, `wiki/index.md` update, `_meta/log.md` 4-entry append; 0 raw moves since clippings already triaged)
  - All committed cards pass §9 gate (or were declined via edit-before-commit)
  - All `[[wikilinks]]` either resolve or are flagged in preview-summary.md as lazy-backfill candidates
  - PR also includes `docs/decisions/ADR-023-vault-layout-v3.md` §Evidence correction: *"3 clippings moved" → "4 clippings moved"* (off-by-one identified in brainstorm C9)
  - M_C2.4 GitHub issue closed
- **Dependencies**: Slice 3 (skill must exist to invoke); Slice 2 (ADR-card subtype frontmatter spec referenced if any of the 4 clippings cite ADRs)
- **Suggested milestone**: M_C2.4
- **Suggested labels**: `vertical-slice`, `cascade-c2`, `dogfood`, `vault-content`
- **Notes**: Dogfood is the load-bearing validation. Friction observations (skill miscategorization, §9 gate over/under-decline, MOC-creation heuristics) feed M_C2.5 retro and v2 ADR backlog (O17–O20). PR scope: ADR-023 evidence correction only (vault writes are filesystem-only per C2).

### Slice 5: M_C2.5 — Sprint retro + Vertical B unblock

- **Goal**: Close Cascade C2 sprint with retro capturing what shipped + friction + L1 promotion candidates; unblock Vertical B (python-ml-uv L3 template).
- **Acceptance**:
  - `retros/sprint-2-vertical-c2.md` (or equivalent path per C1 ADR-003 routing) authored with sections: what shipped (M_C2.1–M_C2.4), brainstorm-design-validation (which decisions held vs. shifted), friction observations, L1 promotion candidates queued in `cascade-system/queue/pending-review.md`, v2 backlog (O17–O20) explicitly named
  - Retro `Status: closed`
  - Vertical B handoff (`docs/handoffs/cascade-b-template-python-ml-uv.md`) updated: blocker note removed (per C4 B-unblock gate triggered by C2 retro close)
  - All M_C2.x GitHub issues closed; M_C2.x milestone closed
  - Clean working tree at retro close (per ADR-008)
- **Dependencies**: Slices 1–4 all closed
- **Suggested milestone**: M_C2.5
- **Suggested labels**: `vertical-slice`, `cascade-c2`, `retro`, `unblock`
- **Notes**: B-unblock is the sprint's external promise (per C4 + handoff §6 don't-bundle-with-B-edits). Retro authoring routes through `@sprint-review` if invoked from Cascade A horizontal heartbeat; otherwise direct authorship by Cascade C2.

## 12 — Out of scope (explicit deferral list)

Things considered and deliberately deferred, with revisit-trigger. Distinct from §3 non-goals: §3 is about scope boundary; this is about deferral with a concrete re-entry condition.

- **Scheduled flush / cron / session-hook distillation** (N1) — deferred because Windsurf has no `SessionStart` / `PreCompact` / `SessionEnd` hooks (GA-3); revisit when platform ships them. External cron is not a substitute (doubles infrastructure).
- **Fully-automatic conversation-triggered distillation** (N2, §10 O17) — deferred to v2 as a **second activation mode on the same skill**, not as a new skill; revisit when `@vault-distill` has 10+ successful invocations OR Windsurf ships SessionEnd hook (whichever first).
- **Distillation of `originals/` content** (N3, §10 O20) — deferred because v1 scope is `raw/` only; revisit when first concrete `originals/` distillation use case emerges (likely candidate: thesis-note distillation into `wiki/concepts/research/`). Future ADR will scope a privacy-scoping mechanism (`--scope=originals/software/projects/<repo>/` flag with per-invocation user consent).
- **Generalized project-namespacing across all `wiki/sources/<type>/` subtypes** (N4, §10 O19) — deferred because v1 scopes project-namespacing to ADR cards only; revisit when 2nd source type reveals project-scope collision. Future ADR will cite ADR-030 as precedent and extend the namespacing pattern to that source type only.
- **`@vault-distill --undo` mode** (N5, §10 O18) — deferred because ADR-011 (supersession over deletion) forbids plain delete; undo semantically means supersession-with-link which needs design. Revisit when first wrong-card-on-disk incident occurs.
- **Immediate full backfill of 29 existing cascade-system ADRs** (N6) — deferred because forward-only + lazy-backfill is the chosen policy; revisit when un-carded-ADR-citation backlog crosses ~10 (Q2b debt-trigger escalation to `cascade-system/queue/pending-review.md`).
- **Git versioning of the vault** (N7) — deferred per ADR-023 §Consequences; revisit when multi-machine-collaboration pressure emerges (e.g., Cascade runs on a second machine, or rollback-from-mistake becomes a real risk).
- **Multi-machine vault sync beyond iCloud** (N8) — deferred per ADR-022 + ADR-023 architecture; revisit when iCloud sync proves insufficient (e.g., non-Apple machine enters the workflow).
- **Topic-based invocation `@vault-distill topic=<x>`** (§10 O3) — deferred; revisit when `@vault-research` has stable topic→`raw/` source mapping.
- **Semantic-similarity duplicate detection** (§10 O12 v2) — v1 = SHA-256 only; revisit when M_C2.4 dogfood or subsequent runs reveal exact-hash-match insufficient for real dedup needs.
- **`--merge <slug>` flag for SHA-256 divergence recovery** (§10 O9 v2) — v1 remediation is `--discard` + fresh re-invoke; revisit if divergence friction accumulates across runs.

## 13 — Sources

- **Brainstorm**: `docs/prompts/stages/m_c2-1-brainstorm.md` (canonical — all design decisions traced here with rationale)
- **Related ADRs (cited during brainstorm + this PRD)**:
  - `docs/decisions/ADR-003-strict-docs-structure.md` — artifact path routing (C1)
  - `docs/decisions/ADR-006-skill-frontmatter-schema.md` — SKILL.md frontmatter contract (Slice 3 acceptance)
  - `docs/decisions/ADR-008-sprint-review-requires-clean-working-tree.md` — retro close gate (Slice 5 acceptance)
  - `docs/decisions/ADR-009-adr-numbering-allocation.md` — ADR-030 NNN reservation (Slice 2)
  - `docs/decisions/ADR-011-record-retention-policy.md` — supersession over deletion (G2, §10 O18)
  - `docs/decisions/ADR-014-l1-canonical-storage-paths.md` — skill L1 path (G1, Slice 3)
  - `docs/decisions/ADR-017-propose-extension-intake-channel.md` — skill authoring route (Slice 3)
  - `docs/decisions/ADR-018-release-discipline-cluster.md` — `@release-manager` PR lifecycle (G6)
  - `docs/decisions/ADR-022-obsidian-cli-selection.md` — CLI primitives + iCloud failure mode (C8, §7 concern 2)
  - `docs/decisions/ADR-023-vault-layout-v3.md` — load-bearing vault spine + AGENTS.md (§1, G6, C9 off-by-one correction target in Slice 4)
  - `docs/decisions/ADR-024-vault-research-skill.md` — READ bookend (G1 symmetry target)
  - `docs/decisions/ADR-025-grill-me-vault-integration.md` — READ-path ambient integration precedent
  - `docs/decisions/ADR-026-to-prd-vault-sources-integration.md` — §13 Sources citation discipline (this PRD follows it)
  - `docs/decisions/ADR-027-recalibrate-vault-drift-signal.md` — READ-path drift detection (complements G3 ADR↔vault posture)
  - `docs/decisions/ADR-028-obsidian-context-priming-rule.md` — session-start ambient load (READ-path completeness)
  - `docs/decisions/ADR-029-verify-l1-path-drift-sweep.md` — L1 verification gate (Slice 2 + Slice 3 acceptance)
  - ADR-030 — reserved at M_C2.2; to be authored in Slice 2
- **Vault-resident raw sources** (pre-distillation; no `[[wikilink]]` target yet — creating those targets is the M_C2.4 dogfood goal):
  - `~/Projects/obsidian/second-brain/raw/clippings/2026-05-04_i-built-self-evolving-claude-code-memory-w-karpathys-llm-knowledge-bases.md` — canonical Karpathy-pattern reference; frames the knowledge-base-compiler architecture this vertical implements
  - `~/Projects/obsidian/second-brain/raw/clippings/2026-05-04_karpathys-wiki-vs-open-brain-one-fails-when-you-need-it-most.md` — §9 quality-gate rationale + "decline to ingest" discipline
  - `~/Projects/obsidian/second-brain/raw/clippings/2026-05-04_karpathys-obsidian-rag-plus-claude-code-equal-cheat-code.md` — Obsidian + LLM integration pattern; informs §7 tempdir-preview approach
  - `~/Projects/obsidian/second-brain/raw/clippings/2026-05-04_i-used-karpathy's-autoresearch-to-train-an-llm.md` — auto-research pattern; v2 conversation-trigger reference (§10 O17)
- **Vault schema** (load-bearing for skill design):
  - `~/Projects/obsidian/second-brain/_meta/AGENTS.md` §1 architecture, §2 routing, §5 frontmatter, §8 ingest workflow, §9 quality gate, §10 multilingual, §11 guardrails — directly shape §7 architecture concerns 1–5
- **L1 skills consulted**:
  - `~/.codeium/windsurf/skills/vault-research/SKILL.md` — READ bookend; symmetry target per G1
  - `~/.codeium/windsurf/skills/grill-me/SKILL.md` — artifact-shape pattern (brainstorm → PRD flow)
  - `~/.codeium/windsurf/skills/to-prd/SKILL.md` — this skill's procedure; followed section-by-section
- **External-web-only references** (not ingested into vault — potential deferred-ingest candidates for future `@vault-distill` runs):
  - Karpathy LLM wiki gist: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
  - Obsidian CLI v1.12.7+ (per ADR-022 selection) — command reference consulted for `obsidian read`, `obsidian search`, `obsidian backlinks`, `obsidian files` primitives
- **Handoff**: `docs/handoffs/cascade-c-obsidian.md` — parent handoff for Vertical C (Sprint 2); Cascade C2 is a follow-on slice addressing the READ/WRITE asymmetry surfaced after Vertical C merged.
- **Parent plan**: `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` — strategic gap analysis that named this vertical (Gap Analysis §Q5).

---

*Authored via `@to-prd` Hybrid-mode drafting flow on 2026-05-04. Plan artifact: `~/.windsurf/plans/m_c2-1-prd-drafting-f61a99.md`.*
