# Cascade C2 Vault Write Path — Brainstorm

**Phase**: brainstorm
**Phase set**: cascade-system meta-repo verticals (no `phases.yaml`; `M_C2.x` milestones = phase sequence)
**Status**: Approved (user-review gate passed 2026-05-04 per `@grill-me` step 8)
**Date**: 2026-05-04
**Milestone**: [M_C2.1 — Decision sprint](https://github.com/ReebalSami/cascade-system/issues/63)
**Handoff**: `cascade-system/docs/handoffs/cascade-c2-vault-write-path.md`
**Parent plan**: `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md`
**Grill artifact**: this file
**PRD artifact**: `cascade-system/docs/prompts/stages/m_c2-1-prd.md` (produced by `@to-prd` in next phase)

---

## Problem

Sprint 2 Vertical C built the **vault READ path**:

- `@vault-research` auxiliary skill (ADR-024) — structural ranking over the vault for topic-scoped research
- `@grill-me` step 1.6 vault-context load (ADR-025) — ambient research at brainstorm time
- `@to-prd` §13 vault-sources integration (ADR-026) — PRD citations reach into the vault
- `/recalibrate` 7th drift signal (ADR-027) — detects PRD↔vault staleness
- `obsidian-context-priming` workspace rule (ADR-028) — session-start schema load ambient

These primitives let Cascade *read* the vault, but **the vault's `wiki/` layer is empty**. Nothing is being distilled INTO it. Per `_meta/AGENTS.md` §1, `wiki/` is Cascade-authored territory — the layer where "LLM-distilled durable knowledge" lives — yet no mechanism exists to write to it. The READ/WRITE asymmetry breaks the intended vault architecture.

Karpathy's LLM-wiki gist and the 4 Karpathy-adjacent clippings at `~/Projects/obsidian/second-brain/raw/clippings/` (surfaced during M2C.2 vault migration) describe a *knowledge-base-compiler* pattern: sources get ingested, distilled into typed wiki entries (concepts, entities, sources, syntheses), quality-gated per `_meta/AGENTS.md` §9, and cross-linked via `[[wikilinks]]` to enable LLM-augmented retrieval. The pattern is load-bearing for the vault's design but has no implementation.

This vertical closes the asymmetry by designing and shipping a vault WRITE path. M_C2.1 (this phase) decides the architecture; M_C2.2 ratifies the ADR↔vault relationship; M_C2.3 authors the chosen skill; M_C2.4 dogfoods it on the 4 Karpathy clippings, producing the vault's first `wiki/` content; M_C2.5 closes the sprint.

## Goals

- **G1** — A user-invoked skill, `@vault-distill`, bookends `@vault-research` (READ ↔ WRITE symmetry). One skill archetype, two directions, same utility tier.
- **G2** — Every distillation run passes the AGENTS.md §9 quality gate BEFORE anything reaches disk. *Decline low-signal / untrustworthy / out-of-scope / duplicate* is a pre-write decision, not post-hoc cleanup.
- **G3** — The ADR↔vault relationship (Posture B) is ratified in a dedicated ADR (ADR-030) with a self-demonstrating dogfood card and a sustainable backfill policy.
- **G4** — Project-scoped namespacing is built into the ADR-card shape from day 1. As cascade-system, Vertical B (python-ml-uv L3), Vertical D (thesis), and future projects each ship their own ADRs, their vault cards stay unambiguously separated at both structural and lexical levels.
- **G5** — M_C2.4 dogfood produces the vault's first `wiki/` content (~12–15 files realistically, merging shared concepts/entities across the 4 Karpathy clippings) — all passing §9 gate, all Obsidian-rendered and wikilink-resolved, all reviewed via dry-run preview before commit.
- **G6** — Write operations are pure filesystem (vault is not git-versioned per ADR-023 §Consequences). No git mutations against the vault. Repo-side artifacts (ADR-030 body, SKILL.md, PRD, retro) land via `@release-manager` per ADR-018.
- **G7** — The write path evolves along a named v2 path (conversation-trigger as a second activation mode on the same skill) without requiring new architecture.

## Non-goals (v1, explicit out-of-scope)

- **N1** — Scheduled flush / cron / session-hook distillation (Gap Analysis §Q5 Option 2). Blocked by GA-3 — Windsurf has no `SessionStart` / `PreCompact` / `SessionEnd` hooks. Revisit when platform ships them; external cron doubles infrastructure (host process, secrets, CLI auth, schedule state) for marginal benefit.
- **N2** — Fully-automatic conversation-triggered distillation (Gap Analysis §Q5 Option 3). Named as v2 candidate in §Open questions below; not committed. Will be added as a second activation site on the same skill when the skill is battle-tested — not as a new skill.
- **N3** — Distillation of `originals/` content (user-authored life artifacts). Skill refuses at boundary with explicit error message pointing to the queue → ADR escape hatch. Future ADR when a real use case emerges (e.g., thesis-note distillation into `wiki/concepts/research/`).
- **N4** — Generalized project-namespacing across all `wiki/sources/<type>/` subtypes (papers, READMEs, meetings, courses, voice memos). v1 scopes project-namespacing to ADR cards only. Future ADR citing ADR-030 as precedent when a second source type hits the same pressure.
- **N5** — `@vault-distill --undo` mode. ADR-011 (supersession over deletion) forbids plain delete; any undo shape is supersession-with-link, which has real semantic differences from "revert the write". v2 work item. Design deferred to PRD §Open questions.
- **N6** — Immediate full backfill of 29 existing cascade-system ADRs into `wiki/sources/adrs/cascade-system/`. Forward-only + lazy-backfill is the policy. Debt-trigger escalation fires if un-carded-ADR-citation backlog grows past ~10 ADRs across open `wiki/` work.
- **N7** — Git versioning of the vault. ADR-023 §Consequences explicitly deferred this. Unchanged here. `@vault-distill` operates on filesystem only.
- **N8** — Multi-machine vault sync beyond iCloud. ADR-022 + ADR-023 establish iCloud as the sync mechanism; this vertical doesn't change that.

## Constraints

- **C1 — Cascade-system is a meta-repo vertical.** No `phases.yaml`; M_C2.x milestones are the phase sequence. Artifact paths follow ADR-003 strict-docs-structure: brainstorm + PRD land at `docs/prompts/stages/`, ADR at `docs/decisions/`, retro at `retros/`.
- **C2 — Vault is not git-versioned.** All vault writes are pure filesystem mutations. No commit discipline at vault level. `@release-manager` discipline applies only to repo-side artifacts.
- **C3 — Bundle anti-pattern enforced (per `@update-horizontal` SKILL.md + handoff §8).** ADR-030 (M_C2.2) and `@vault-distill` SKILL.md (M_C2.3) ship in SEPARATE PRs. Bundling them violates one-ADR-per-logical-change.
- **C4 — B-unblock gate.** Vertical B (python-ml-uv L3 template) stays blocked until C2's retro (M_C2.5) closes. No edits to B's handoff in any C2 PR.
- **C5 — AGENTS.md §9 quality gate is load-bearing.** Every distilled card must pass: high signal, trustworthy, in-scope for some MOC/domain, non-duplicate. Gate fires pre-write.
- **C6 — AGENTS.md §11 guardrails apply.** Skill must never: modify `raw/<books|papers|clippings|voice|courses>/` post-triage; modify `originals/` without explicit instruction; delete a file (supersede with link per ADR-011); create a wiki page without at least one `[[wikilink]]` in/out; bulk-create without confirmation; store secrets in any vault file.
- **C7 — AGENTS.md §10 multilingual posture.** Folder names English-only lowercase-kebab-case. Wiki pages default to English regardless of source language (translate if needed). Source language in frontmatter `language:`.
- **C8 — iCloud materialization risk.** ADR-022 documented the iCloud 0-byte-stub failure mode. The `obsidian` CLI interacts with Obsidian's in-memory index, so this class of failure doesn't surface there — but filesystem-direct operations (e.g., `ls`, `find`) can still return ghost-empty results. Verified during this phase's orientation: `list_dir` on `raw/clippings/` returned 0 items while `ls -la` returned 4 files. Skill must use `obsidian files` / `obsidian read` primitives for source reads, not direct filesystem globs, to stay on the reliable path.
- **C9 — 4 Karpathy clippings are the M_C2.4 dogfood corpus** (verified present at `raw/clippings/` during orientation): `2026-05-04_i-built-self-evolving-claude-code-memory-w-karpathys-llm-knowledge-bases.md` (canonical, 23 KB), `2026-05-04_karpathys-wiki-vs-open-brain-one-fails-when-you-need-it-most.md` (largest, 47 KB), `2026-05-04_karpathys-obsidian-rag-plus-claude-code-equal-cheat-code.md` (16 KB), `2026-05-04_i-used-karpathy's-autoresearch-to-train-an-llm.md` (16 KB). ADR-023 §Evidence currently says "3 clippings moved" — off-by-one; correction note planned in M_C2.4 PR.

## Approaches considered

One section per question resolved during the grill. Approach lists + the chosen path with rationale. Full grill transcript is the chat history for this session; this brainstorm captures the decisions, trade-offs, and eliminated candidates.

### Q1 — Architecture of the write path

Three candidates (from Gap Analysis §Q5 + handoff §5 Q1):

| # | Shape | Trade-offs |
|---|---|---|
| **1** | **User-invoked `@vault-distill` skill** — explicit `@vault-distill <path>` invocation per source or batch | **Pros**: READ/WRITE symmetry with `@vault-research`; auditable per-invocation (every distillation is a discrete decision point with §9 gate); shippable today; cognitive load trivial (same shape as `@vault-research`, opposite direction). **Cons**: requires user to remember to invoke; easy to accumulate raw/ backlog if user defers. |
| **2** | **Scheduled flush** — cron job / session hook triggers distillation automatically on a schedule or lifecycle event | **Pros**: zero user friction; Cole-Medin + Karpathy pattern. **Cons**: **blocked by GA-3** — Windsurf has no session hooks. External cron doubles infrastructure (host process, CLI auth, secrets management, schedule state). Unblock path outside our control. |
| **3** | **Conversation-triggered** — end-of-`@grill-me` / end-of-`@to-prd` hook asks *"distill findings X/Y/Z to `wiki/concepts/<topic>`?"* | **Pros**: only-write-what-you-learned posture; automatic without being pushy. **Cons**: depends on session-end detection which Windsurf also lacks. Can be partially simulated by making each relevant skill explicitly call `@vault-distill` at its transition step. |

**Chosen**: **Option 1** with Option 3 named as a v2 evolution path (not a new skill — a second activation mode on the same skill, called from end-of-skill transition points when that infrastructure exists).

Rationale:

- **READ/WRITE symmetry** — `@vault-research` READS, `@vault-distill` WRITES. Same utility tier, same invocation shape, paired mental model. The two skills bookend each other in the vault docstring, making the vault's CRUD surface complete at the skill layer.
- **Auditable per-invocation** — every distillation is a discrete operation. The AGENTS.md §9 quality gate has a decision point to decline AT. Option 2's auto-flush and Option 3's silent hook both risk bypassing the gate or surfacing declines asynchronously.
- **Karpathy-pattern alignment** — Karpathy's gist describes invocation *per source* (*"when you read something interesting, run the compiler against it"*). That's Option 1. Option 2 is Cole-Medin's addition on top of Karpathy, and Cole-Medin's addition is precisely what GA-3 blocks.
- **Option 2 is eliminable** — its only advantage (zero user friction) is outweighed by GA-3's unblock path being outside our control.
- **Option 3 is mostly value-on-top-of-Option-1** — if `@vault-distill` already exists as a skill, making `@grill-me` step 9 (transition) call `@vault-distill --from-findings=<session-findings-file>` is a small edit to `@grill-me`, not new architecture. Ship Option 1; evolve to Option 3 when the skill is battle-tested.

### Q2 — ADR ↔ vault posture

Three candidates (from handoff §5 Q2):

| Posture | Shape | Verdict |
|---|---|---|
| **A** | **Separation** — `cascade-system/docs/decisions/` canonical; vault never sees ADRs | Zero cost now; migration cost later (backfill when `wiki/*` concepts start citing ADRs). Gets A→B transition pain for ~30+ ADRs. |
| **B** | **Unified discovery** — ADR files canonical at `docs/decisions/`; vault adds `wiki/sources/adrs/<project>/<card>.md` summary cards with 1-line summary + `[[wikilinks]]` to touched concepts/entities + `source_path` back-pointer | One-canonical-home rule preserved; incremental authoring; dogfood-friendly (ADR-030 declares posture and has its own card). |
| **C** | **Vault-first** — ADRs authored inside vault, rendered/exported to `docs/decisions/` for git history | Render step per commit + merge-conflict surface + CI pipeline we don't have. Over-engineered for current size. Inverts canonical authority from repo to vault. |

**Chosen**: **Posture B**.

Rationale:

- **Preserves one-canonical-home** per AGENTS.md §6 (vault) and the equivalent discipline in cascade-system docs. ADR body lives once; vault card is a summary + typed links that the ADR's concepts/entities touch. Updates don't fork.
- **The first wiki content this vertical produces will want to cite ADRs.** M_C2.4 distills Karpathy clippings → concept cards → those cards want `[[wikilink]]` paths to ADR-022 / ADR-023 / ADR-024 (the foundational pattern). Posture A forces ugly relative paths like `../../../cascade-system/docs/decisions/ADR-023-...` in wiki markdown. Posture B gives clean `[[cascade-system_adr-023-vault-layout-v3]]`.
- **Bootstrap has a pleasing dogfood**: ADR-030 declares Posture B, and ADR-030's own vault card is the first instance.
- **C eliminated**: its only advantage (vault-as-source-of-truth) doesn't fit a system where cascade-system repo is the decision-authority and every ADR merges via `@release-manager` (that's the audit trail).

### Q2a — Project-scoping shape for ADR cards

Three candidates (surfaced by user constraint *"categorize somehow to which project, also in the names"*):

| # | Shape | Path + Wikilink |
|---|---|---|
| **A** | **Subdirectory only** | `wiki/sources/adrs/cascade-system/adr-023-vault-layout-v3.md` — `[[adr-023-vault-layout-v3]]` |
| **B** | **Filename prefix only (flat dir)** | `wiki/sources/adrs/cascade-system_adr-023-vault-layout-v3.md` — `[[cascade-system_adr-023-vault-layout-v3]]` |
| **C** | **Both** | `wiki/sources/adrs/cascade-system/cascade-system_adr-023-vault-layout-v3.md` — `[[cascade-system_adr-023-vault-layout-v3]]` |

**Chosen**: **Shape C** (subdirectory + filename prefix, `_` separator).

Rationale:

- **User's *"also in the names"* constraint** rules out Shape A alone. Project must appear both structurally (for grouping and Obsidian tree-view) and lexically (in the basename itself).
- **Survives paste-and-carry** — pasting `[[cascade-system_adr-030-adr-vault-posture]]` into chat, a blog post, or the cascade-system README shows project scope without vault context. Shape A's bare `[[adr-030-adr-vault-posture]]` loses this.
- **Survives file moves** — if a card is accidentally dragged out of its folder during Obsidian reorganization (easy to do in mobile Obsidian), Shape A becomes orphan-with-ambiguous-name; Shape C stays self-identifying by basename. Belt-and-suspenders against a real vault failure mode.
- **Separator `_`** matches existing vault practice — the 4 Karpathy clippings already use `_` at the date-slug boundary (`2026-05-04_i-built-self-evolving...`). Consistency > novelty.
- **Not `-` separator** — ADR slugs themselves contain `-`; project+ADR with `-` creates parsing ambiguity (*"is `cascade-system-adr` the project, or is `cascade` the project and `system-adr` part of the ADR slug?"*).

**Frontmatter spec** (new — to be formalized in ADR-030 + `_meta/AGENTS.md` §5):

```yaml
---
type: source
source_type: adr
project: cascade-system
adr_id: "030"                         # zero-padded string for sort stability
title: "ADR↔vault posture"
date: 2026-05-04
source_path: cascade-system/docs/decisions/ADR-030-adr-vault-posture.md
status: Accepted                      # | Superseded by <id> | Proposed | Deprecated
supersedes: []
superseded_by: null
linked_adrs: []                       # cross-project ADR citations as [[wikilinks]]
extracted_concepts: []                # wiki/concepts/<domain>/<sub>/*.md backrefs
extracted_entities: []                # wiki/entities/<type>/*.md backrefs
tags: [source, adr, cascade-system]
---
```

### Q2b — Backfill scope for existing 29 cascade-system ADRs

Three options:

| # | Shape | Cards in ADR-030's PR | Cost signal |
|---|---|---|---|
| **1** | **Forward-only + lazy backfill** | 1 (ADR-030 itself) | Lowest |
| **2** | **Immediate full backfill** | 30 (030 + 001–029) | Highest — ~5–8h card authoring; bundle-anti-pattern strain; 30-file review surface |
| **3** | **Immediate selective backfill** (~15 cross-cutting ADRs) | 16 (030 + selective) | Middle — requires upfront cross-cutting-vs-feature taxonomy, itself a sub-decision |

**Chosen**: **Option 1** with explicit unresolved-wikilink tolerance and a debt-trigger escalation.

Rationale:

- **Bundle anti-pattern** — ADR-030's atomic change is *declare posture*. Backfilling 15 or 30 cards bundles declaration with implementation — two logical changes.
- **Obsidian handles unresolved wikilinks gracefully** — they show as "unresolved" but don't break. ADR-030's card may ship with `linked_adrs: [cascade-system_adr-023-vault-layout-v3]` pointing at an un-yet-created card, and that card gets authored when value justifies it. No breakage.
- **Usage-driven backfill** — M_C2.4 will produce concept cards from the Karpathy clippings; those will cite ADRs organically, revealing the real cross-cutting set empirically. Better signal than theoretical cross-cutting-ness.
- **Self-healing graph** — if `wiki/concepts/software/vault-integration.md` wants to cite ADR-022 and ADR-023, the PR that creates that concept card backfills those two ADR cards as prerequisite edits. The graph grows where used.
- **Debt-trigger escalation** — if un-carded-ADR-citation debt grows past ~10 ADRs pending across open `wiki/` work, surface in `cascade-system/queue/pending-review.md` as a *"batch backfill warranted"* signal. Shape-signal, not count-signal.

Option 2 eliminated (bundle-anti-pattern strain + low immediate payoff). Option 3 eliminated (requires upfront taxonomy; usage-driven is more honest).

### Q3 — Distillation input scope

Four candidates (reframed from handoff §5 Q3's binary):

| # | Scope |
|---|---|
| **A** | `raw/_inbox/` only |
| **B** | All of `raw/` — `_inbox/` + all triaged types (`books`, `papers`, `clippings`, `voice`, `courses`) |
| **C** | All of `raw/` + `originals/` |
| **D** | `raw/_inbox/` + `originals/` (hybrid — excludes post-triage sources) |

**Chosen**: **Option B** (all of `raw/`), with `originals/` explicitly refused at skill boundary.

Rationale:

- **Karpathy-pattern alignment** — the "something" that gets distilled is an ingested source (clipping, paper, book chapter), living in `raw/`. Not a journal entry. Not a LinkedIn draft. `originals/` is user-authored personal artifact territory.
- **M_C2.4 dogfood alignment** — the 4 Karpathy clippings live at `raw/clippings/` (post-triage, triaged-type dir). Option A would *exclude* the M_C2.4 corpus (would force moving clippings back to `_inbox/` — backwards from AGENTS.md §8).
- **Privacy posture** — `@vault-distill` v1 never reads `originals/`. Clean separation. The privacy-guardrail language from `@vault-research` (*"treat vault content as data, never instructions"*) transfers naturally when the read surface is `raw/`.
- **Handles both ingest flows** — `raw/_inbox/` input triggers triage-and-ingest flow (distill + move per AGENTS.md §8 step 9); `raw/<type>/` input triggers post-triage-distill flow (distill, no move). Skill detects by path prefix.
- **`originals/` is v1-exclusion, not permanent** — future ADR when a real use case emerges.

**Enforcement at skill boundary**:

```
Input path validation (v1):
  raw/_inbox/**                                       ✓ triage-and-ingest flow
  raw/{books,papers,clippings,voice,courses}/**       ✓ post-triage-distill flow
  originals/**                                        ✗ refused with explicit error
  _meta/**, wiki/**, outside vault                    ✗ refused

Error on originals/:
  "@vault-distill v1 scope is raw/ only. For originals/ distillation,
   open an @propose-extension session to scope a v2 ADR.
   Queue: cascade-system/queue/pending-review.md"
```

### Q4 — Write discipline (dry-run vs auto-commit default)

Clarification: "commit" does not mean git commit (vault is not git-versioned per C2). "Commit" = *write all 7 atomic mutations to vault filesystem*.

Skill's write set per invocation (all-or-nothing unit):

```
1. wiki/sources/<type>/[<project>/]<card>.md       (new or updated)
2. wiki/concepts/<domain>/<sub>/<concept>.md       (new or updated — multiple possible)
3. wiki/entities/<type>/<name>.md                  (new or updated — multiple possible)
4. wiki/mocs/MOC - <topic>.md                      (updated if new topic emerges)
5. wiki/index.md                                   (updated — auto-maintained)
6. _meta/log.md                                    (appended per AGENTS.md §8)
7. raw/_inbox/<file> → raw/<type>/<year>-<slug>    (moved, IF input was _inbox/)
```

Three discipline candidates:

| # | Mode | Shape |
|---|---|---|
| **A** | **Auto-commit default** | Skill writes all 7 mutations immediately; user reviews post-hoc. Optional `--dry-run` flag inverts. |
| **B** | **Dry-run default** | Skill outputs preview, writes nothing; explicit `--commit` triggers atomic write. |
| **C** | **Interactive per-file default** | Skill prompts at each proposed write: *"Create `<path>`? [y/N/edit]"* |

**Chosen**: **Mode B** (dry-run default, `--commit` flag).

Rationale:

- **AGENTS.md §9 quality gate is PRE-write, not post-write.** Karpathy's load-bearing rule is decline-before-store. Once a card is on disk, ADR-011 supersession-over-deletion kicks in — the bad card becomes permanent cruft with a supersession link. Dry-run gives the decline point a real surface.
- **v1 conservatism during first dogfood** — M_C2.4 is the FIRST run of a brand-new skill on a brand-new corpus producing ~12-15 new wiki files. Every output needs scrutiny. If 3/15 cards are miscategorized under auto-commit, cleanup is permanent (ADR-011 forbids plain delete).
- **`make-sure-it-works` alignment** — evidence over claims; dry-run produces the evidence before the write.
- **Trust tier is earnable** — after 10+ successful runs with stable "user-approves-unchanged" pattern, flip default via v2 ADR (debt-trigger style). Don't guess the trust tier now.
- **`--commit` flag preserves eager-mode escape hatch** for power users doing bulk backfill.

Mode A eliminated (wrong timing for §9 gate). Mode C eliminated (prompt fatigue defeats the purpose; becomes opt-in via `--interactive` flag).

### Q4a — Preview output shape

Three candidates:

| # | Shape | Bulk-scale | Render fidelity | Edit-before-commit |
|---|---|---|---|---|
| **1** | Inline-chat Markdown | Fails (>10 files drowns chat) | Cascade renderer imperfect for nested YAML+MD | Re-run with corrections |
| **2** | Tempdir (`_meta/.preview/<timestamp>_<slug>/`) with Obsidian-openable files | Excellent (scales to 100+ files) | Obsidian-native (ground truth) | Direct edit — changes carry through to commit |
| **3** | Unified-diff-style terminal output | Excellent at summary-level | Low (no full render) | Re-run with corrections |

**Chosen**: **Option 2** as v1 primary mode, with Option 3's diff-style summary embedded as a mandatory `preview-summary.md` entry point inside each preview dir. Option 1 available via `--preview=inline` flag for small single-file runs.

Concrete tempdir shape:

```
_meta/.preview/2026-05-04T143022_karpathy-clippings-distill/
├── preview-summary.md                 ← MANDATORY entry point (Option 3 semantics)
├── mirrors/
│   ├── wiki/sources/adrs/cascade-system/cascade-system_adr-030-adr-vault-posture.md
│   ├── wiki/concepts/software/vault-integration.md
│   ├── wiki/entities/people/karpathy.md
│   ├── wiki/mocs/MOC - llm-wiki.md
│   ├── wiki/index.md                  ← updated version
│   └── _meta/log-append.md            ← proposed log append text
└── raw-move.manifest                  ← describes raw/_inbox/→raw/<type>/ moves
```

`preview-summary.md` content includes:

- Invocation (input path + flags + timestamp)
- Proposed writes count (new / edits / moves / appends)
- Per-card summary (path, size, concepts extracted, entities extracted, linked ADRs — including unresolved wikilinks)
- AGENTS.md §9 quality gate decisions (PASSED + reasons, DECLINED + reasons)
- Unresolved wikilinks (flags for lazy-backfill per Q2b)
- Commit + discard instructions

Rationale:

- **Obsidian-native rendering is non-negotiable** during review-heavy dogfood. User reviews in Obsidian (same renderer as post-commit). Zero ambiguity.
- **Edit-before-commit is a killer feature** — user edits preview file directly, commit carries the edit. Options 1 and 3 require re-invoking with correction hints.
- **Scales to bulk** — M_C2.4 is ~12-15 files; future backfill runs potentially 40+; Option 2 handles 100+ cleanly. Option 1 caps at ~10.
- **Summary-first preserved** via mandatory `preview-summary.md` — progressive disclosure without losing overview. Option 3's strength preserved inside Option 2's structure.
- **`_meta/.preview/` is legitimate vault infrastructure** — `_meta/` is schema/tooling territory; `.preview/` with dot-prefix signals transient/ignorable status. AGENTS.md §2 routing table gets one new row documenting it in ADR-030's diff.

**Implementation detail** (PRD-phase territory, noted here for context):

- **Preview slug**: `<ISO8601-timestamp>_<input-basename-or-dir>` — sortable, human-readable, collision-free
- **Cleanup policy**: `--commit` deletes preview dir on success; `--discard <slug>` for explicit abandonment; auto-GC previews >7 days old on next invocation
- **Commit coupling**: `@vault-distill --commit <slug>` or `@vault-distill --commit` (implicit latest)
- **Divergence detection**: preview captures SHA-256 hashes of target files at preview time; commit re-checks; refuses with clear message if any target mutated

## Design decisions (consolidated reference)

Single-table view of the resolved design for downstream skills (`@to-prd`, `@write-skill`) to reference:

| Dimension | Decision |
|---|---|
| Invocation model | User-invoked skill `@vault-distill` bookending `@vault-research` |
| v2 evolution path | Option 3 conversation-triggered mode as second activation site on same skill (v2 candidate, not committed) |
| Input scope | All of `raw/` — `_inbox/` (triage-and-ingest) + triaged types (post-triage-distill); skill auto-detects by path prefix |
| `originals/` posture | Refused at skill boundary with error message pointing to queue → ADR escape hatch |
| Write discipline | Dry-run default; `--commit` flag for eager write |
| Preview mechanism | Tempdir at `_meta/.preview/<timestamp>_<slug>/` with Obsidian-openable file mirrors |
| Preview summary | Mandatory `preview-summary.md` entry point in each preview dir (diff-style overview) |
| Small-run fallback | `--preview=inline` flag for chat-inline preview of small single-file runs |
| Commit coupling | `@vault-distill --commit [<slug>]`; SHA-256 divergence detection |
| Edit-before-commit | Supported — user edits preview files directly in Obsidian; edits carry through |
| Preview cleanup | `--commit` deletes on success; `--discard <slug>` abandons; auto-GC >7d old on next invocation |
| Quality gate timing | Pre-write, surfaced in `preview-summary.md` with PASSED/DECLINED + reasons per source |
| ADR ↔ vault posture | Posture B — unified discovery via `wiki/sources/adrs/<project>/<card>.md` summary cards |
| ADR card path | `wiki/sources/adrs/<project-slug>/<project-slug>_adr-<NNN>-<kebab-slug>.md` |
| ADR card wikilink | `[[<project-slug>_adr-<NNN>-<kebab-slug>]]` |
| Project-scope separator | `_` (underscore), matching vault's existing date-slug separator practice |
| ADR card frontmatter | `type` + `source_type: adr` + `project` + `adr_id` (padded string) + `title` + `date` + `source_path` + `status` + `supersedes` + `superseded_by` + `linked_adrs` + `extracted_concepts` + `extracted_entities` + `tags` |
| Backfill scope (existing 29 ADRs) | Forward-only + lazy backfill + debt-trigger at ~10 un-carded ADRs with pending citations |
| Unresolved-wikilink tolerance | Accepted — Obsidian renders as "unresolved"; cards get authored on citation pressure |
| `_meta/AGENTS.md` extensions | §2 new row for `_meta/.preview/` routing; §5 new subtype for `source_type: adr` frontmatter (both in ADR-030 PR) |

## ADR-030 PR shape (M_C2.2 preview)

Four files, one atomic posture decision:

```
cascade-system/docs/decisions/INDEX.md                               (edit — ADR-030 row reserved per ADR-009 BEFORE body authoring)
cascade-system/docs/decisions/ADR-030-adr-vault-posture.md           (new — ADR body)

<vault — filesystem-only, no git>
wiki/sources/adrs/cascade-system/cascade-system_adr-030-adr-vault-posture.md   (new — dogfood card)
_meta/AGENTS.md                                                      (edit — §2 routing + §5 frontmatter spec)
```

## M_C2.3 skill shape (preview — PRD-phase territory)

`@vault-distill` SKILL.md authored via `@propose-extension` → `@write-skill` flow per ADR-017. Authored at `~/.codeium/windsurf/skills/vault-distill/SKILL.md` (L1 canonical path per ADR-014). `@verify-l1` must be green before PR merges. Sources consulted will include the 4 Karpathy clippings + `@vault-research` SKILL.md (the READ bookend). PR includes outside-repo-edit acknowledgment in body.

## M_C2.4 dogfood shape (preview)

Invocation:

```
@vault-distill raw/clippings/
```

Expected preview dir contents (approximate, subject to §9 gate decisions):

- 4 source cards (one per clipping): `wiki/sources/clippings/karpathy_*.md`
- 1 Karpathy entity card (merged across 4 sources): `wiki/entities/people/karpathy.md`
- 5–8 shared concept cards: `wiki/concepts/software/{vault-as-compiler,quality-gate-pre-write,llm-wiki-pattern,...}.md`
- 1 MOC: `wiki/mocs/MOC - llm-wiki.md`
- `wiki/index.md` update
- `_meta/log.md` append (4 ingest entries)
- 0 raw moves (clippings already at `raw/clippings/`, not `_inbox/`)

**Secondary deliverable**: M_C2.4 PR includes a correction note in ADR-023 §Evidence for the clipping-count off-by-one (3 → 4).

## Open questions (for `@to-prd` to nail down in the PRD phase)

### Skill-signature questions

- **O1** — Exact v1 flag set for `@vault-distill`. Proposed: `--commit [<slug>]`, `--discard <slug>`, `--preview=tempdir|inline`, `--interactive`, `--verbose`, `--dry-run` (redundant with default, but available as explicit), `--clean-previews`. PRD confirms or trims.
- **O2** — Invocation argument shapes. Path (`@vault-distill raw/clippings/<file>.md`), directory (`@vault-distill raw/clippings/`), glob (`@vault-distill raw/clippings/karpathy-*.md`), multi-path (`@vault-distill raw/clippings/a.md raw/papers/b.md`). Which shapes v1 supports.
- **O3** — Topic-based invocation. Does v1 support `@vault-distill topic=karpathy-wiki` (skill searches `raw/` for matches)? Probably v2. PRD decides.

### Concept/entity extraction mechanics

- **O4** — Concept extraction prompt shape. How does the skill decide what counts as a "concept" vs a mere mention? What's the prompt to the LLM? How does the skill reconcile with existing `wiki/concepts/` vocabulary (avoid near-duplicate concept cards)?
- **O5** — Entity extraction scope. Which entity types per AGENTS.md §2 (*"people/organizations/tools/places"*) — is `wiki/entities/<type>/` always one of these four? What's the canonical-name resolution for variant spellings ("Andrej Karpathy" vs "Karpathy" vs "A. Karpathy")?
- **O6** — Shared-concept/entity merging across multi-source runs. When `@vault-distill raw/clippings/` processes 4 clippings that all mention Karpathy, how does the skill produce ONE `wiki/entities/people/karpathy.md` card with all 4 source-backrefs, rather than 4 duplicate cards? PRD specifies the merge algorithm.
- **O7** — MOC update semantics. When does a new MOC get created vs a concept added to an existing MOC? What's the heuristic? (Probably: if 3+ concepts share a topic that doesn't have an existing MOC, propose one; otherwise link to existing.)

### Error handling + edge cases

- **O8** — Source file malformed / unreadable / too-large. What's the skill's behavior? Error out? Skip with warning in preview? Configurable threshold?
- **O9** — Divergence detection failure recovery. If preview captured SHA-256 at t=0 and commit at t=1 detects mutation, what does the user do? Skill should suggest a concrete remediation (`@vault-distill --merge <slug>` that re-generates preview on top of current state? Or just "re-run with fresh preview"?).
- **O10** — Concurrent invocations. What if the user runs `@vault-distill` twice in quick succession? Two preview dirs + possible overlap? Skill should either serialize or refuse with clear message.
- **O11** — `raw/_inbox/` move conflicts. What if `raw/clippings/2026-05-04-karpathy-wiki.md` already exists when the skill tries to move a `_inbox/` file to that path? Conflict resolution strategy.

### Quality-gate mechanics (AGENTS.md §9)

- **O12** — How does the skill detect duplicate content? Hash-based on body? Semantic similarity to existing `wiki/sources/`? PRD specifies the dedup heuristic.
- **O13** — "Low signal" and "untrustworthy" are subjective. Does the skill decide, or does it always present to user for decline-or-accept decision? Probably: skill *flags* potential declines in preview-summary.md with reasons, user decides.
- **O14** — Out-of-scope detection. How does the skill know what's "in scope for a current MOC or domain"? Does it ever refuse on its own, or always pass through for user decision?

### Log format + audit trail

- **O15** — `_meta/log.md` append format. AGENTS.md §8 shows `## [YYYY-MM-DD] ingest | <slug>` — is that exact? What fields in the append (source count, card count, flags used, preview-slug reference for audit)?
- **O16** — Preview-to-commit audit trail. Does the committed cards' frontmatter reference the preview-slug they came from? For reproducibility.

### v2 candidates (named but not designed in v1)

- **O17** — Option 3 conversation-trigger: exactly when does `@grill-me` / `@to-prd` end-of-session call `@vault-distill`? What's the trigger heuristic (*"findings worth keeping"*)? How does the user opt in/out per-session?
- **O18** — `@vault-distill --undo` shape. Given ADR-011 forbids delete, what does undo semantically mean? Supersession-with-link? Marker tag? PRD scopes the v2 design space.
- **O19** — Generalized project-namespacing. When a second source type hits the same project-scope pressure, what's the ADR shape? (Probably: ADR-NNN citing ADR-030 as precedent, extending the namespacing pattern to that source type only.)
- **O20** — `originals/` scope expansion. What use case triggers it? What's the privacy-scoping mechanism? (Probably: per-invocation scope flag like `--scope=originals/software/projects/<repo>/` with explicit user consent per run.)

### Backfill debt-trigger specifics

- **O21** — Debt-trigger surface. What's the exact queue-entry shape for the *"batch backfill warranted"* signal? Who drains it — `@sync-github` at next milestone close, `@sprint-review` at sprint close, or an explicit `@vault-distill --backfill` skill variant?

## Next phase

`spec` via `@to-prd` — produces `cascade-system/docs/prompts/stages/m_c2-1-prd.md`.

`@to-prd` inputs this brainstorm artifact + handoff + parent plan + 4 Karpathy clippings as sources (per ADR-026 §13 vault-sources integration). The PRD formalizes answers to §Open questions O1–O21 above, writes vertical slices sized for one GitHub issue each, and closes issue [#63](https://github.com/ReebalSami/cascade-system/issues/63) in the same PR that lands both artifacts.

PRD transition is user-explicit per `@grill-me` step 9 — not auto-invoked. User opens the next phase when ready.

**Promoted to PRD**: `docs/prompts/stages/m_c2-1-prd.md` on 2026-05-04. Next phase: M_C2.2 — ADR-030 ratification PR (via `@propose-extension` → `@release-manager`).

## Provenance

- **Grill session**: `@kickoff` → plan `~/.windsurf/plans/cascade-c2-kickoff-orientation-f5b3b1.md` → `@grill-me` M_C2.1 Q1–Q4a (2026-05-04 interactive session)
- **Handoff**: `cascade-system/docs/handoffs/cascade-c2-vault-write-path.md`
- **Parent plan**: `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md`
- **Predecessor shape**: `cascade-system/docs/handoffs/cascade-c-obsidian.md` (vault READ path vertical)
- **Related ADRs cited during grill**:
  - ADR-009 (NNN reserve-in-INDEX-first)
  - ADR-011 (supersession over deletion)
  - ADR-014 (L1 canonical storage paths)
  - ADR-017 (`@propose-extension` intake)
  - ADR-018 (release-discipline cluster)
  - ADR-022 (Obsidian CLI selection + iCloud failure mode)
  - ADR-023 (vault layout v3 — load-bearing parent for this vertical)
  - ADR-024 (`@vault-research` skill — READ bookend)
  - ADR-025 (`@grill-me` vault integration)
  - ADR-026 (`@to-prd` vault sources)
  - ADR-027 (`/recalibrate` vault drift signal)
  - ADR-028 (obsidian-context-priming workspace rule)
  - ADR-029 (`@verify-l1` sweep)
  - ADR-030 (this vertical's output — reserved at M_C2.2)
- **Vault corpus (M_C2.4 dogfood)**: 4 Karpathy clippings at `~/Projects/obsidian/second-brain/raw/clippings/`
- **Vault schema**: `~/Projects/obsidian/second-brain/_meta/AGENTS.md` — 13-section load-bearing schema (especially §2 routing, §5 frontmatter, §8 ingest workflow, §9 quality gate, §11 guardrails)
- **L1 skills consulted**: `~/.codeium/windsurf/skills/grill-me/SKILL.md` (this skill — step 6 artifact shape), `~/.codeium/windsurf/skills/vault-research/SKILL.md` (READ bookend studied for WRITE skill design)
