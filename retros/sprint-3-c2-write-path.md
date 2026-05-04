# Sprint 3 — Vertical C2 — Vault write path — Retro

**Status**: closed
**Sprint**: 3 — Vertical C2 (vault WRITE path; bookends Vertical C's READ path)
**Cascade**: C2 (vertical; owns the vault WRITE path deferred from Vertical C as queue entries GA-1 + GA-2)
**Parent plan**: `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` (strategic gap analysis authored during Vertical C close)
**Handoff**: `docs/handoffs/cascade-c2-vault-write-path.md` (authored by horizontal Cascade A post-Vertical-C-drain; PR #62)
**Kickoff orientation plan**: `~/.windsurf/plans/kickoff-cascade-c2-4fddcf.md` (this session's `@kickoff` orientation)
**Milestone**: GitHub `Sprint 3 Vertical C2 — Vault write path` (#3 — closes on this retro's merge)
**Issues**: 5 (M_C2.1 #63, M_C2.2 #64, M_C2.3 #65, M_C2.4 #66, M_C2.5 #67)
**PRs**: 5 merged into `main` via `@release-manager` — #62 (handoff), #68 (M_C2.1 artifacts), #69 (M_C2.2 ADR-030), #70 (M_C2.3 ADR-031 + SKILL.md), #71 (M_C2.4 ADR-023 §Evidence correction), plus this retro PR
**Date opened**: 2026-05-04
**Date closed**: 2026-05-04

## 1 — Summary (what shipped)

Vertical C2 closed the vault **WRITE path** deferred from Vertical C (queue entries GA-1 + GA-2). Bookends `@vault-research` (READ, ADR-024) with `@vault-distill` (WRITE, ADR-031). Five milestones shipped in dependency order:

1. **M_C2.1 — Decision sprint (brainstorm + PRD)** — Authored `docs/prompts/stages/m_c2-1-brainstorm.md` and `docs/prompts/stages/m_c2-1-prd.md`. PRD §7 locked the solution architecture: Option 1 user-invoked `@vault-distill` skill (Option 3 deferred to v2 as additive `--from-findings` flag on same skill per PRD §10 O17; Option 2 blocked by GA-3 Windsurf session-hooks). PRD §10 resolved 21 open questions with best-guesses + re-evaluation triggers. PRD §11 decomposed into 5 shippable slices 1:1 with M_C2.1–M_C2.5. Issue #63 closed; PR #68 merged.
2. **M_C2.2 — ADR-030 ADR↔vault posture** — Authored `docs/decisions/ADR-030-adr-vault-posture.md` (Posture B + Shape C namespacing: `wiki/sources/adrs/<project-slug>/<project-slug>_adr-<NNN>-<slug>.md`; lazy-backfill forward-only with ~10-ADR debt-trigger). Vault mutations (non-git per C2): dogfood card `cascade-system_adr-030-adr-vault-posture.md` + `_meta/AGENTS.md` §2 + §5 schema extensions. Closes ADR-023 §Deferred "ADR↔vault relationship" item. Issue #64 closed; PR #69 merged.
3. **M_C2.3 — `@vault-distill` SKILL.md + ADR-031** — Authored `~/.codeium/windsurf/skills/vault-distill/SKILL.md` (~290 lines) at L1 canonical path per ADR-014 via `@propose-extension` → `@write-skill` pass-through (PRD answered all 5 intake questions). `adapt-from-all` pass consulted 4 Karpathy clippings + `@vault-research` (READ bookend) + `refs/claude-skills/engineering/llm-wiki/` (direct prior art) + `refs/claude-skills/engineering/autoresearch-agent/` + `refs/superpowers/skills/writing-skills/` (CSO discipline) + vault `_meta/AGENTS.md` schema. Repo-side: ADR-031 + INDEX updates + cheat-sheet count 11→13 + `@write-skill` step 9 drift captured to queue. Manual `@verify-l1` audit passed all 8 steps. Issue #65 closed; PR #70 merged.
4. **M_C2.4 — First distillation run (dogfood)** — Live-fire invocation of `@vault-distill raw/clippings/` against the 4 Karpathy clippings. Produced preview `_meta/.preview/2026-05-04T182540_karpathy-clippings-distill/` (17 mirror files + preview-summary.md + raw-move.manifest). `--commit` applied cleanly: SHA-256 integrity check green, collision check green, 15 new files + `wiki/index.md` rewrite + `_meta/log.md` append. Repo-side: ADR-023 §Evidence off-by-one correction (3→4 clippings). Issue #66 closed; PR #71 merged.
5. **M_C2.6 — This retro** — Closes the vertical. Issue #67 closes on this PR's merge; milestone #3 closes immediately after. GA-1 + GA-2 removed from `queue/pending-review.md` as RESOLVED; GA-3 remains (platform-blocked).

**Verdict**: All 4 open design questions (handoff §5 Q1–Q4) resolved by M_C2.1 PRD; all Q1–Q2 ratified as ADRs (ADR-031 for Q1 architecture; ADR-030 for Q2 posture); Q3 + Q4 operationalized in the `@vault-distill` SKILL.md procedure. First real `wiki/` content (15 new cards in a single distill run) beyond the M_C2.2 ADR-030 dogfood seed. READ ↔ WRITE symmetric pair established with cross-reference docstrings on both sides.

## 2 — Brainstorm/PRD design validation (decisions held vs shifted)

Dedicated section (handoff §9 DoD asks for this) — were the M_C2.1 PRD's 21 best-guesses (O1–O21) correct?

### Held (operationalized as-designed, no friction)

| Question | Best-guess held | Evidence from M_C2.3 + M_C2.4 |
|---|---|---|
| O1 (flags) | `--commit`, `--discard`, `--clean-previews`, `--interactive` core set | M_C2.4 used default (dry-run) + `--commit` — no flag gaps hit |
| O2 (invocation shapes) | Path + directory + multi-path in v1 | M_C2.4 exercised directory (`raw/clippings/`); multi-source dedupe worked |
| O5 (entity types + canonical-name resolution) | 4 types (people/orgs/tools/places); `obsidian search` canonical-name lookup | M_C2.4 produced 2 people (karpathy, cole-medin) + 2 tools (claude-code, obsidian); no conflicts |
| O6 (multi-source merge) | Single-pass extract → normalize → dedupe by canonical name | `karpathy` entity merged cleanly across all 4 sources with all backrefs; **the load-bearing PRD §11 Slice 4 test — passed** |
| O12 (duplicate detection) | SHA-256 on normalized body (v1 only) | Not exercised at 4-source scale (no exact dupes); awaits larger runs |
| O13 (§9 gate as flagging not deciding) | Skill flags with reasons; user decides via delete-from-preview | **Exercised** — autoresearch source flagged out-of-scope; preserved by user choice (not deleted); proved the flag-and-reason path works |
| O14 (out-of-scope detection) | Check against existing MOCs + `_meta/AGENTS.md` §3 domain map; flag + suggest | **Exercised** — source 4 correctly flagged against `MOC - llm-knowledge-bases` scope; [[karpathy]] backref preserved for entity attribution across both patterns |
| O15–O16 (log append + audit trail) | Per `_meta/AGENTS.md` §8 exact format; `preview_slug:` frontmatter on every card | M_C2.4 log-append followed format; `preview_slug: 2026-05-04T182540_karpathy-clippings-distill` stamped on all 15 new cards |

### Shifted (post-M_C2.3 discoveries)

| Question | Original best-guess | What actually happened | Why the shift |
|---|---|---|---|
| O7 (MOC semantics: new vs existing) | Heuristic: ≥3 concepts share a topic → new MOC | Applied as designed (6 concepts share LLM-wiki topic → new `MOC - llm-knowledge-bases`); **but the 3-threshold felt fuzzy**. Captured inline in preview-summary.md as friction — worth empirical calibration | Heuristic worked but leaves judgment room; the "3" is shape, not count. Might re-tune after Vertical D |
| N/A (status frontmatter) | PRD didn't address scope-flag expression in frontmatter | **Self-review caught a contract violation**: source 4 mirror had `status: flagged-out-of-scope` but `_meta/AGENTS.md` §5 only permits `draft\|active\|published\|completed\|archived`. Fixed pre-commit: `status: active` + scope flag preserved via `tags: [out-of-scope-llm-wiki]` + body annotation + `extracted_concepts: []` | Scope flagging (PRD O14) collided silently with contract (§5 status). Candidate `_meta/AGENTS.md` §5 clarification: add explicit `scope: in\|out\|flagged` field OR formalize tag-based convention |
| N/A (naming convention) | PRD referenced §4 "YYYY-<slug>" but didn't confirm application to `wiki/sources/articles/` | Applied as `2026-<slug>.md` (felt right); **not explicitly mandated by §4** which scopes "Sources in raw/ post-triage" only | Candidate `_meta/AGENTS.md` §4 clarification |

### Deferred to future runs (not yet evidence on)

- **O3** (topic-based invocation) — v2 per PRD; not exercised
- **O4** (concept prompt + dedup mechanics) — no near-match flag fired at 4-source scale; re-evaluate at N > 10 or at a run with concept-dense existing wiki
- **O8** (malformed source skip) — all 4 sources read cleanly; untested
- **O9** (SHA-256 divergence recovery) — no divergence occurred; remediation path untested
- **O10** (concurrent invocation) — single-session; untested
- **O11** (raw/_inbox/ move conflicts) — post-triage flow (0 moves); untested
- **O17–O20** (v2 candidates: conversation-trigger, `--undo`, generalized project-namespacing, originals/ scope) — explicitly deferred per PRD §3 + §12
- **O21** (backfill debt-trigger) — 2 un-carded-ADR wikilinks after this run (ADR-031 + whatever ADR-023 revision cites), well below ~10 debt-trigger

## 3 — Friction observations (surfaced during execution)

Captured inline (per `bidirectional-learning-pipe`) — a subset already landed as queue entries in M_C2.3's PR #70 + M_C2.4's PR #71; surfacing here for retrospective visibility.

### 3.1 — `@write-skill` step 9 inaccuracy (captured, in queue)

`@write-skill` SKILL.md step 9 reads *"If L1: no global index file yet (M1.11 may add one); no action needed"*. Authoring `@vault-distill` forced manual updates to `docs/skills/INDEX.md` + `docs/cheat-sheet.md` §1 count. Captured to `queue/pending-review.md` as "Sprint 3 — cascade-system — Cascade C2 — M_C2.3 author (`@write-skill` step 9 inaccuracy)" — trigger for L1 promotion: next `@write-skill` invocation OR M_C2.5 retro. **L1 promotion proposal stays queued** (not drained in this retro — drain happens via Cascade A's horizontal `@sprint-review`, not inside a vertical's retro).

### 3.2 — Scope-flag ↔ status-contract collision (captured, new queue entry below)

Self-review at M_C2.4 preview caught `status: flagged-out-of-scope` violating `_meta/AGENTS.md` §5's `draft|active|published|completed|archived` enum. Fixed pre-commit (status → `active`, flag moved to `tags:` + body). Real example of PRD O13/O14 flagging machinery colliding with vault frontmatter contract. Captured below as a new queue entry.

### 3.3 — `_meta/AGENTS.md` §4 naming ambiguity for `wiki/sources/*`

§4 says *"Sources in raw/ post-triage: `YYYY-<slug>.<ext>` (year-only)"* — which scopes only to `raw/`. Applied by analogy to `wiki/sources/articles/` (`2026-<slug>.md`) without explicit contract backing. Captured below as part of the same queue entry (bundled since both are `_meta/AGENTS.md` clarifications).

### 3.4 — MOC-creation heuristic is shape, not count

PRD O7 uses "≥3 concepts share a topic → new MOC" but at 6 concepts the judgment was still discretionary ("could this cluster under `MOC - home`'s Domains stub?"). Not friction enough to block execution; worth an empirical calibration pass after Vertical D produces another new-MOC case.

### 3.5 — Curly-apostrophe filename (source 4)

`raw/clippings/2026-05-04_i-used-karpathy'’'s-autoresearch-to-train-an-llm.md` uses `U+2019 RIGHT SINGLE QUOTATION MARK`. First attempt at `obsidian read` with straight `'` failed; retry with the actual curly char worked. Captured as minor friction in the vault `_meta/log.md` M_C2.4 entry; no action needed (future clippings will likely preserve source-accurate apostrophes from Obsidian Web Clipper).

### 3.6 — Vault-integrity-check verbosity

The SHA-256 integrity check at `--commit` was thorough but produced verbose terminal output (one line per target). Per PRD §7 this is **intentional** (auditability), but it's worth noting that at larger scales (N > 50) the output becomes noisy. Not a v1 concern.

## 4 — L1 change proposals (queued; NOT drained in this retro)

Per horizontal/vertical split (handoff §7 + `@sprint-review` SKILL.md): L1 mutations route through `@sprint-review` → `@update-horizontal`, not through a vertical's retro. The queue entries below become candidates for Cascade A's next horizontal `@sprint-review` pass.

| Entry | Status after this retro | Candidate L1 change |
|---|---|---|
| `@write-skill` step 9 inaccuracy | Queued in M_C2.3 PR #70 | Update step 9 to instruct `docs/skills/INDEX.md` + `docs/cheat-sheet.md` updates for L1 skills |
| Scope-flag ↔ status-contract collision + §4 wiki/sources naming ambiguity | **NEW entry in this retro's PR** | Two `_meta/AGENTS.md` clarifications: §5 status enum handling for scope flags, §4 naming convention for `wiki/sources/*` |
| MOC-creation heuristic calibration | **NEW entry in this retro's PR** | Empirical-calibration capture — re-evaluate PRD O7 threshold after Vertical D's first new-MOC case |

Three new L1 promotion candidates surface from this vertical. Cascade A's next horizontal `@sprint-review` will triage.

## 5 — Queue updates (this retro's PR)

### Removed from queue as RESOLVED

- **GA-1 (vault WRITE path / `@vault-distill` candidate)** — RESOLVED by M_C2.3 (ADR-031 + SKILL.md) + M_C2.4 (first distill run). No longer deferred; the capability is live.
- **GA-2 (ADR ↔ vault relationship — future ADR proposal)** — RESOLVED by M_C2.2 (ADR-030 Posture B + Shape C + dogfood card). No longer deferred; the posture is ratified.

### Retained in queue

- **GA-3 (Windsurf session-hooks equivalent — Cole Medin pattern)** — REMAINS (platform-blocked). Trigger for re-evaluation unchanged: when Windsurf ships session-boundary callbacks.
- All other queue entries unchanged.

### New entries captured in this retro

- **`_meta/AGENTS.md` §4 + §5 clarifications** (this retro's capture): naming convention for `wiki/sources/*` subtypes + scope-flag expression mechanism
- **MOC-creation-heuristic calibration** (this retro's capture): PRD O7 threshold re-evaluation trigger = Vertical D's first new-MOC case

## 6 — Definition-of-done check (handoff §9)

- [x] ADR-030 accepted; numbered via INDEX-reserve-first per ADR-009 — ✅ (PR #69 merged 2026-05-04)
- [x] Chosen-architecture skill authored; passes `@verify-l1`; `sources_consulted` + `adapted_for` frontmatter filled — ✅ (`@vault-distill`; PR #70 merged; verify-l1 manual audit clean all 8 steps)
- [x] First distillation run produces ≥1 concept card AND ≥1 source card — ✅ (6 concepts + 4 entities + 4 sources + 1 MOC + 1 index + 1 log-append = 15 new files at M_C2.4; verified via `obsidian files path=wiki/`)
- [x] `retros/sprint-3-c2-write-path.md` written; `Status: closed` — ✅ (this file)
- [x] `queue/pending-review.md` GA-1 + GA-2 REMOVED as RESOLVED; GA-3 remains — ✅ (this retro's PR)
- [x] All M_C2.x GitHub issues closed — ✅ (#63, #64, #65, #66 closed via merged PRs; #67 auto-closes on this PR's merge)
- [x] Clean working tree in `cascade-system/`; outside-repo edits acknowledged per PR descriptions — ✅ (PR #70 acknowledged `~/.codeium/windsurf/skills/vault-distill/` + `vault-research/` outside-repo edits per ADR-008 M-2 trade-off; M_C2.2's PR #69 and M_C2.4's PR #71 acknowledged vault-side filesystem writes per PRD §8 C2; this retro's PR has no outside-repo edits)
- [x] **B (`python-ml-uv`) is unblocked only when C2's retro closes** — ⏳ unblocks on **this retro's merge** per handoff §9 final bullet; B's handoff untouched per discipline

## 7 — Carry-forward (open items surviving this vertical)

| Item | Why carry forward | Where tracked |
|---|---|---|
| **Vertical B (`python-ml-uv` L3 template)** unblocked | Per handoff §9 DoD C4 gate: unblocks when this retro closes. Cascade A's next action (when bandwidth allows): `@kickoff docs/handoffs/cascade-b-template-python-ml-uv.md`. B's `phases.yaml` + scaffold can now bake in vault-write phases referencing `@vault-distill` from day 1. | Handoff §9 + this retro §6 final row |
| **Vertical D (thesis dogfood)** still not started | D's handoff is authored when B closes. D consumes B's template + benefits from C + C2's vault layer. First real-world thesis-corpus distillation will stress-test `@vault-distill` at larger N (> 10 sources) — first empirical signal for O3/O4/O12 v2 decisions | Sprint 3+ planning |
| **3 new L1 promotion candidates** from this vertical (see §4) | Cascade A's next horizontal `@sprint-review` triage | `queue/pending-review.md` (this retro's PR) |
| **`@vault-distill` v2 backlog** (PRD §10 O17–O20) | Explicitly deferred; each has a concrete re-entry trigger (10+ successful invocations OR Windsurf session-hooks for O17; first wrong-card-on-disk for O18; 2nd source type for O19; first `originals/` use case for O20) | PRD §12 out-of-scope deferral list |
| **ADR-030 lazy-backfill counter** | 2 un-carded cascade-system ADR wikilinks after M_C2.4 (ADR-031 + forthcoming future ADRs). Well below ~10 debt-trigger. Future `@vault-distill` runs against ADRs will incrementally backfill | `_meta/log.md` M_C2.4 entry + PRD §10 O21 |

## 8 — Sign-off

**Closed**: 2026-05-04

**Phase outcomes**:

- **M_C2.1 (decision sprint)**: PRD approved; 21 open questions best-guessed with re-eval triggers. PR #68.
- **M_C2.2 (ADR-030 ratification)**: Posture B + Shape C; dogfood card + AGENTS.md §2 + §5 extensions. PR #69.
- **M_C2.3 (`@vault-distill` skill)**: ~290-line SKILL.md at L1 canonical path; ADR-031 + INDEX + cheat-sheet + symmetric-pair reference in `@vault-research`; manual `@verify-l1` all 8 steps green; `@write-skill` step 9 drift captured to queue. PR #70.
- **M_C2.4 (first distillation run)**: 15 new wiki files + wiki/index + `_meta/log` via preview-dir + `--commit`; ADR-023 §Evidence off-by-one fixed; dogfood friction observations surfaced inline. PR #71.
- **M_C2.5 (this retro)**: DoD closed; GA-1 + GA-2 resolved; 3 new L1 candidates queued; Vertical B unblocks on merge.

**Follow-ups carried forward** (per §7):

- Vertical B `@kickoff` when bandwidth allows (unblocks on this retro's merge)
- Vertical D handoff authoring when B closes (Sprint 3+ planning)
- 3 new L1 promotion candidates for Cascade A's next horizontal `@sprint-review`
- `@vault-distill` v2 backlog (PRD O17–O20) with concrete triggers

**What this vertical did NOT change** (worth naming so future reviews don't waste cycles re-examining):

- Vault layout stays at v3 (ADR-023) — this vertical added cards to the spine, didn't modify it
- `@vault-research` structural-ranking composite (ADR-024 0.4/0.4/0.2 weights + 4-hop MOC-distance cap + 3–7 result clamp) unchanged — WRITE bookend didn't force a READ recalibration
- Obsidian CLI (ADR-022) remains the sole vault access tool — no MCP re-evaluation needed
- `global_rules.md` size unchanged (still at 5972/6000 chars; this vertical added zero L1 rules — `@vault-distill` is a skill)
- Vertical B handoff untouched (per handoff §9 final bullet; B-unblock discipline lives in this retro's DoD, not in cross-vertical edits)

---

*Template source: `~/.windsurf/templates/_shared/retro-template.md`. Skill used: none (retro authored directly — `@sprint-review` is for horizontal-layer drains, not vertical retros). Pairs with `@update-horizontal` only via the queue entries captured in §4–§5 which land at Cascade A's next horizontal `@sprint-review`.*
