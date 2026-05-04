# ADR-031: `@vault-distill` auxiliary skill

**Status**: Accepted
**Date**: 2026-05-04
**Milestone**: M_C2.3 ([#65](https://github.com/ReebalSami/cascade-system/issues/65)) — *Chosen-architecture skill*
**Parent plan**: `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` §Q5 Option 1 (cheapest v1; bookends `@vault-research`)
**Source PRD**: `docs/prompts/stages/m_c2-1-prd.md` §7 (solution overview) + §10 O1–O21 (best-guess resolutions) + §11 Slice 3
**Source handoff**: `docs/handoffs/cascade-c2-vault-write-path.md` §5 Q1
**Source ADR**: [ADR-030](./ADR-030-adr-vault-posture.md) — Posture B ADR-card namespacing this skill emits

## Context

Sprint 2 Vertical C shipped the vault **READ path** (ADRs 022–028: CLI selection, layout v3, `@vault-research`, three integration points, priming rule). The vault's `wiki/` layer — per ADR-023 §4 *"Cascade writes `wiki/`"* — has no mechanism to receive content. `MOC - home.md` is a stub; no other `wiki/` content exists. The Cascade-distilled durable-knowledge promise of ADR-023 §4 is unfulfilled.

Gap analysis §Q2 row #1 (high severity) + queue entry GA-1 captured the gap. Gap analysis §Q5 enumerated three candidate architectures (Option 1 user-invoked, Option 2 scheduled/session-hook, Option 3 conversation-trigger); the M_C2.1 PRD §7 locked Option 1 with Option 3 named as an additive v2 evolution on the same skill (not a separate skill). Option 2 is blocked by GA-3 (Windsurf lacks session-boundary callbacks).

M_C2.2 / ADR-030 resolved the ADR↔vault posture (Posture B + Shape C namespacing). Cards now have a canonical path shape. The missing piece is the skill that **produces** those cards (and the other `wiki/` shapes — concepts, entities, sources, syntheses, MOCs).

## Decision

Author L1 skill `@vault-distill` at canonical path `~/.codeium/windsurf/skills/vault-distill/SKILL.md` per ADR-014.

**Skill shape** (utility-archetype; bookends `@vault-research`):

- **Invocation**: `@vault-distill <path | dir | multi-path>` — dry-run by default; produces tempdir preview at `_meta/.preview/<ISO8601-ts>_<slug>/` with `mirrors/` + `preview-summary.md` + `raw-move.manifest`
- **Commit**: `@vault-distill --commit [<slug>]` — atomic apply; SHA-256 target-path integrity re-check before write; deletes preview dir on success; stamps `preview_slug:` frontmatter on every committed card
- **Additional modes**: `--discard <slug>`, `--clean-previews` (auto-GC > 7 days), `--interactive`
- **Scope enforcement**: `raw/**` only in v1; refuses `originals/**`, `_meta/**`, `wiki/**`, outside-vault paths at the boundary. `raw/_inbox/**` inputs trigger triage-and-ingest flow (captured in `raw-move.manifest`); `raw/{books,papers,clippings,voice,courses}/**` inputs trigger post-triage-distill
- **§9 quality gate**: flagging-not-deciding (per PRD O13). Skill marks decline candidates with reasons in `preview-summary.md` (`signal` / `trust` / `scope` / `duplicate` axes); user actions decline by deleting the mirror file from the preview dir before `--commit`
- **Multi-source dedupe**: single-pass extract → normalize → dedupe by canonical name → emit ONE card per canonical entity/concept with all source backrefs (resolves PRD O6; load-bearing test is the M_C2.4 4-clipping Karpathy merge)
- **CLI-only reads**: `obsidian read` / `obsidian files` / `obsidian search` — never `ls` / `find` / `cat` on vault paths (iCloud ghost-empty risk per ADR-022 + queue entry "iCloud materialization")
- **ADR-card emission**: when distilling an ADR, path = `wiki/sources/adrs/<project-slug>/<project-slug>_adr-<NNN>-<slug>.md` + frontmatter `source_type: adr` + `project:` + `adr_id:` + `source_path:` back-pointer per ADR-030 Shape C
- **Privacy guardrails**: inherited verbatim from `@vault-research` (prompt-injection resistance; `raw/_inbox/` handling; path validation; read-with-intent; never-store-secrets)

**Termination**: dry-run produces preview + exits. `--commit` applies atomic mutation set + deletes preview. `--discard` removes preview. Hard-gate refusal (CLI unavailable / scope violation / active-preview collision / SHA-256 divergence): explicit reason + exit.

**Symmetric-pair docstring reference**: `@vault-research` SKILL.md description updated to cite `@vault-distill` as WRITE bookend; `@vault-distill` SKILL.md description cites `@vault-research` as READ bookend. Per PRD §6 M_C2.3 AC.

**Per PRD §8 C2**: vault is not git-versioned. All vault writes are pure filesystem mutations. `@release-manager` discipline applies only to repo-side artifacts (this ADR, INDEX updates, cheat-sheet + skills-INDEX bumps); SKILL.md lives under `~/.codeium/windsurf/` which is unversioned per the deferred M-2 storage decision. PR description acknowledges the outside-repo edit per ADR-008 clean-tree gate's M-2 trade-off (as PR #55 established for `@vault-research`).

## Alternatives considered

| Candidate | Shape | Verdict |
|---|---|---|
| **Option 1 — `@vault-distill` user-invoked skill** (chosen) | Explicit invocation per corpus; dry-run tempdir preview + `--commit`; bookends `@vault-research` | Adopted — PRD §7 solution overview is the authoritative spec; cheapest v1 per gap-analysis §Q5 |
| **Option 2 — Scheduled flush / session-hook** | Karpathy / Cole Medin automated pattern; cron or Windsurf SessionEnd callback | Rejected (deferred per PRD §3 N1) — Windsurf lacks session callbacks (GA-3); external cron is infra overhead without the session-boundary trigger signal |
| **Option 3 — Conversation-triggered** | At end of `@grill-me` / `@to-prd` session, prompt *"write findings X/Y/Z to wiki?"* | Named as v2 evolution path (PRD §10 O17) — additive `--from-findings=<file>` flag on the same skill, not a new skill. v1 procedure designed extensibly so v2 doesn't require skill replacement |
| **Chat-based interactive flow** (llm-wiki precedent) | LLM discusses proposed updates in chat, user approves each inline | Rejected — scales poorly to multi-source runs (4-clipping dogfood would produce 40+ chat-turns). Preview-dir + edit-before-commit (ADR-023 §11 discipline) is the Cascade-specific adaptation |
| **`--skip-preview` / `--force-commit` mode** | Skip dry-run when user is confident | Rejected (anti-pattern per SKILL.md) — defeats the killer feature (edit-before-commit). If a v2 ADR argues for a trust tier, author it then; don't erode v1 discipline preemptively |
| **Auto-decline §9 violations** | Skill decides what passes the gate; silently drops low-signal cards | Rejected (PRD O13) — defeats user oversight; the gate is a **flag-and-reason** operation; user actions via delete-from-preview |
| **Semantic-similarity duplicate detection** | Embedding-cosine match against existing `wiki/` cards | Rejected for v1 (PRD O12) — v1 is SHA-256 on normalized body (deterministic, no embedding-model dep). Viable v2 if exact-hash misses meaningful duplicates |
| **Leave as-is (do nothing)** | Vault `wiki/` stays empty indefinitely | Rejected — ADR-023 §4 promise unfulfilled; gap-analysis §Q2 high severity; blocks Vertical B (per handoff §9 DoD C4 B-unblock gate) |

## Consequences

**Enables**:

- First real `wiki/` content can be produced (M_C2.4 dogfood: 4 Karpathy clippings → ~10 wiki files with merged Karpathy entity card)
- ADR-030 Posture B becomes executable — ADRs can be distilled into `wiki/sources/adrs/<project>/` cards via the standard skill rather than hand-authored (dogfood card for ADR-030 itself already landed via hand-edit during M_C2.2; future ADRs route through this skill)
- `@vault-research` has a symmetric WRITE bookend; READ → WRITE loop closes
- Vertical B (python-ml-uv L3 template) unblocks when M_C2.5 retro closes per handoff §9 DoD — B's `phases.yaml` can reference vault-write phases that use this skill
- Future projects (Vertical D thesis, subsequent cascade-system ADRs, any future L3 template) inherit the ADR-card emission shape from day 1 (project-namespacing pays off across the constellation per PRD G4)

**Constrains**:

- Vault must follow ADR-023 conventions (`raw/` + `originals/` + `wiki/` spine; `_meta/AGENTS.md` schema; `[[wikilinks]]`; MOCs) — pre-v3 vaults don't work with this skill
- Obsidian app must be running for the CLI socket (graceful refusal per ADR-022 line 83 if down)
- SHA-256 divergence false-positives on legitimate user edits between preview and commit — mitigation: `--discard` + re-invoke (PRD §9 Risks row)
- v1 deliberately excludes `originals/` distillation (N3); users expecting thesis-note-into-`wiki/concepts/research/` will need a future ADR with privacy-scoping mechanism (deferred to the concrete use case per PRD §10 O20)
- v1 deliberately excludes multi-machine sync / git versioning of the vault (N7 + N8); iCloud remains the sync mechanism per ADR-022 + ADR-023
- `@write-skill` step 9 docstring becomes inaccurate — it says *"If L1: no global index file yet (M1.11 may add one); no action needed"*, but `docs/skills/INDEX.md` exists (Sprint 1.5.4) and MUST be updated when an L1 skill lands. **Captured as a queue entry for L1 drain** (see §Future work)

**Future work** (queue captures from this ADR):

- **`@write-skill` step 9 inaccuracy** — docstring says *"no global index file yet"* but `docs/skills/INDEX.md` exists per Sprint 1.5.4. On next `@update-horizontal` against `@write-skill` (or at a future `@sprint-review`), update step 9 to instruct "update `docs/skills/INDEX.md` + `docs/cheat-sheet.md` skill count". Captured to queue as low-severity drift per `bidirectional-learning-pipe`
- Empirical calibration of §9 gate decline heuristics — PRD §10 O13 flagged; M_C2.4 dogfood produces the first evidence
- `--merge <slug>` flag for SHA-256 divergence recovery (PRD §10 O9 v2 candidate) — author if friction emerges during M_C2.4 or subsequent runs
- `--undo <slug>` via ADR-011 supersession-with-link (PRD §10 O18 v2 candidate) — author on first wrong-card-on-disk incident
- Conversation-trigger v2 activation mode (PRD §10 O17) — `--from-findings=<session-findings-file>` flag on the same skill; author when `@vault-distill` has 10+ successful invocations OR Windsurf ships SessionEnd hook (whichever first)

## Sources consulted

**Saturation-driven survey** (6 cluster groups — full list in `~/.codeium/windsurf/skills/vault-distill/SKILL.md` frontmatter `sources_consulted`):

- **Cluster 1 — Direct prior art (adopted structure, rejected chat-flow)**: `refs/claude-skills/engineering/llm-wiki/SKILL.md` (MIT, vendored) — Karpathy LLM-wiki pattern; three-layer raw/wiki/schema; ingest/query/lint operations; iron-rule no-edit-raw; entity/concept/source/synthesis page categories
- **Cluster 2 — Audit-trail discipline (adopted `preview_slug` pattern)**: `refs/claude-skills/engineering/autoresearch-agent/SKILL.md` (MIT, vendored) — Karpathy autoresearch `program.md` + `results.tsv` audit-trail idea; rejected experiment-loop structure (one-shot per invocation, not metric-optimization)
- **Cluster 3 — SKILL.md authoring discipline (CSO + structure)**: `refs/superpowers/skills/writing-skills/SKILL.md` (vendored) — description-is-triggers-not-workflow; keep-inline-<50-lines heuristic; flat namespace
- **Cluster 4 — READ-bookend structure (lifted verbatim)**: `~/.codeium/windsurf/skills/vault-research/SKILL.md` (own) — SKILL.md section structure, privacy guardrails, CLI-preference pattern from ADR-022
- **Cluster 5 — Source content (M_C2.4 dogfood corpus + scope disambiguation)**: 4 Karpathy clippings at `~/Projects/obsidian/second-brain/raw/clippings/2026-05-04_*` — Cole Medin variant of Karpathy pattern (daily-logs, compounding-knowledge-loop); wiki-vs-open-brain framing for §9 gate; Obsidian+LLM tooling stack; autoresearch scope disambiguation (this skill is NOT autoresearch)
- **Cluster 6 — Vault schema (contract this skill reads + writes against)**: `~/Projects/obsidian/second-brain/_meta/AGENTS.md` §5 (frontmatter including ADR-030 `source_type: adr` extension), §8 (ingest workflow), §9 (quality gate), §11 (guardrails)

**Related ADRs**:

- ADR-006 (SKILL.md frontmatter schema) — applied
- ADR-009 (NNN reserve-in-INDEX-first) — applied; ADR-031 row reserved before authoring
- ADR-011 (supersession over deletion) — applied: wiki-card supersession flow replaces silent overwrite
- ADR-014 (L1 canonical paths) — applied; `~/.codeium/windsurf/skills/vault-distill/SKILL.md`
- ADR-017 (`@propose-extension` intake) — applied: PRD M_C2.1 answered the 5 interview questions (build-vs-capture = build; type = skill; layer = L1; problem = vault WRITE path asymmetry; activation = auto). `@propose-extension` was a pass-through dispatch to `@write-skill`; no additional intake surface needed
- ADR-022 (Obsidian CLI selection) — applied: skill calls `obsidian read`, `obsidian files`, `obsidian search`, `obsidian backlinks`
- ADR-023 (Vault layout v3) — applied: scope enforcement, §9 quality gate, §11 guardrails
- ADR-024 (`@vault-research` auxiliary skill) — WRITE bookend of this READ skill; symmetric-pair docstrings
- ADR-029 (`@verify-l1` path-drift sweep) — applied: manual verify-l1 audit run against the authored skill (all 8 steps green)
- ADR-030 (ADR↔vault posture) — applied: ADR-card emission shape

## Empirical-test plan

`@vault-distill` is exercised by **M_C2.4 first distillation run** (next milestone in Vertical C2). Live-fire signal:

- **Pass**: `@vault-distill raw/clippings/` against the 4 Karpathy clippings produces a preview at `_meta/.preview/<ts>_karpathy-clippings-distill/` containing ≥10 mirror files covering 4 source cards + 1 merged Karpathy entity card + 5–8 shared concept cards + 1 MOC + `wiki/index.md` update + `_meta/log.md` 4-entry append. User reviews in Obsidian, optionally edits (miscategorized paths, §9 decline flags), runs `--commit <slug>`. All cards pass §9 gate; all `[[wikilinks]]` either resolve or are flagged as lazy-backfill candidates. Multi-source dedupe produces ONE `karpathy.md` entity card, not 4. ADR-023 §Evidence off-by-one (3 → 4 clippings moved) corrected in the same PR.
- **Fail / capture for v2**: user reports (a) over-decline on legitimate content (tune §9 flagging); (b) under-decline on low-signal content (tune §9 flagging); (c) SHA-256 divergence false-positive on legitimate user edit (author `--merge <slug>` per PRD O9); (d) miscategorization patterns (refine Procedure step 4 concept/entity extraction heuristics); (e) dedupe misses (e.g., "Andrej Karpathy" vs "Karpathy" — flag resolution via edit-before-commit). Each capture lands in `queue/pending-review.md` for M_C2.5 retro triage.

## Provenance

Authored 2026-05-04 by Cascade C2 as Slice 3 of the Sprint 3 Vertical C2 vault write path (M_C2.3). Promoted from approved PRD `docs/prompts/stages/m_c2-1-prd.md` §11 Slice 3. Pairs with ADR-030 (Posture B ADR-card shape this skill emits) + ADR-024 (`@vault-research` READ bookend). Precedent pattern: PR #55 (ADR-024 + SKILL.md authoring for `@vault-research`). Next milestone: M_C2.4 dogfood run ([#66](https://github.com/ReebalSami/cascade-system/issues/66)).
