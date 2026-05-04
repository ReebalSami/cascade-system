# Sprint 2 — Vertical C — Obsidian integration — Retro

**Status**: closed
**Sprint**: 2 — Vertical C (Obsidian integration)
**Cascade**: C (vertical, owns the Obsidian-integration scope)
**Plan refs**: 10 plan files at `~/.windsurf/plans/cascade-c-*` and `~/.windsurf/plans/m2c*` (see §4 Friction for high-churn note); this retro authored against `~/.windsurf/plans/cascade-c-obsidian-kickoff-f832e8.md`
**Handoff**: `docs/handoffs/cascade-c-obsidian.md` (parent vertical) + `docs/handoffs/cascade-c-m2c2-vault-execution.md` (M2C.2 milestone)
**Milestone**: GitHub `Sprint 2 Vertical C — Obsidian integration` (#2 — closes on this retro's merge)
**Issues**: 6 (M2C.1 #41, M2C.2 #42, M2C.3 #43, M2C.4 #44, M2C.5 #45, M2C.6 #46)
**PRs**: 6 merged into `main` via `@release-manager` (#47 M2C.1, #48 M2C.2 handoff, #49 M2C.2 ADR + restructure, #50 M2C.3, #51 M2C.4, #52 M2C.5) plus this retro PR
**Date**: 2026-05-04

## 1 — Summary (what shipped)

Vertical C closed the Obsidian-integration scope from handoff `docs/handoffs/cascade-c-obsidian.md`. Six milestones shipped in order:

1. **M2C.1 — Obsidian access architecture** — Selected first-party `obsidian` CLI v1.12.7 over 4 community MCP architectures via ADR-022. Issue #41 closed; PR #47 merged.
2. **M2C.2 — Vault layout convention** — Designed and executed in-place restructure of pre-existing chaos-state vault into v3 spine (`_meta/raw/originals/wiki`); ratified iCloud-symlink as canonical access path. ADR-023 authored. Issue #42 closed; PRs #48 (handoff promotion) + #49 (ADR + restructure) merged.
3. **M2C.3 — L1 proposals: `@grill-me`, `@to-prd`, `/recalibrate`** — Three saturation-driven queue entries authored at `queue/pending-review.md`. Each makes an existing implicit Obsidian touchpoint concrete via the `obsidian` CLI subroutine, citing ADR-022 + ADR-023. Issue #43 closed; PR #50 merged.
4. **M2C.4 — `obsidian-context-priming` rule proposal** — Watcher-archetype rule queued at `queue/pending-review.md`. Treats vault content as data-not-instructions; excludes `raw/_inbox/` from auto-priming per the prompt-injection literature cluster. Issue #44 closed; PR #51 merged.
5. **M2C.5 — `@vault-research` auxiliary skill proposal** — Utility-skill archetype queued at `queue/pending-review.md`. Recency × tag-overlap × MOC-distance composite ranking; deliberately structural (not semantic) per ADR-023's hand-curated vault. Issue #45 closed; PR #52 merged.
6. **M2C.6 — This retro** — Closes the vertical. Issue #46 closes on this PR's merge; milestone closes immediately after.

**Verdict**: All architectural deliverables shipped. Five L1 proposals are queued for Cascade A's next horizontal `@sprint-review` drain (none drained by Vertical C — that is not vertical scope per handoff §4). Verticals B and D can now use `obsidian://` artifact paths in their projects' `phases.yaml`.

## 2 — MCP rationale (M2C.1 → ADR-022)

Handoff §3 M2C.1 framed the milestone as *"Discover and configure an Obsidian MCP"*. The evaluation pivoted: of the 4 candidate community MCP architectures (REST-API plugin, filesystem-direct, native-plugin, DIY) and the first-party `obsidian` CLI, only the CLI gave deterministic vault access without the silent-404 failure mode that filesystem-direct hits on iCloud-evicted file stubs. The CLI talks to Obsidian's running-app in-memory index, so iCloud materialization is transparently handled by Obsidian itself rather than by the access tool.

The pre-registered `obsidian-mcp-server` entry in `~/.codeium/windsurf/mcp_config.json` is annotated as superseded; the runtime architecture is now the CLI hitting Obsidian's running-app in-memory index. ADR-022 captures the full decision tree.

**`adapt-from-all` consultation** (per ADR-022 §Sources consulted): 4 community MCP candidates surveyed (`jlevere/obsidian-mcp-plugin`, `devwhodevs/engraph`, `lobehub Vault as MCP`, Promptfire plugin); first-party CLI shipped in Obsidian 1.12 (stable v1.12.7).

**Architectural departure from the handoff** is correct behavior — handoffs are starting hypotheses based on the Sprint-1-close landscape, not contracts. ADR-011 supersession-over-deletion handled the discrepancy: the parent handoff was status-updated, not rewritten.

## 3 — Vault layout (M2C.2 → ADR-023)

ADR-023 locked the v3 spine: `_meta/` (schema/templates/scripts) + `raw/` (inbound source-of-truth, immutable post-triage) + `originals/` (user-authored, 7 domains: journal, stories, academic, software, languages, professional, personal) + `wiki/` (Cascade-distilled durable knowledge, Karpathy type-first ontology: entities/concepts/sources/synthesis/mocs).

**Strict layer ownership** prevents user-vs-Cascade write collisions:

| Layer | Owner | Mutability |
|---|---|---|
| `_meta/` | Cascade + Reebal | Co-evolved via `@propose-extension` |
| `raw/` | Reebal captures + Cascade triages | Immutable post-triage |
| `originals/` | Reebal | Cascade never writes without explicit instruction |
| `wiki/` | Cascade | Reebal reads, rarely edits |

**Migration shape**: in-place restructure of the pre-existing chaos-state vault (~120 files across 10 legacy top-levels). 11 legacy items (10 folders + 1 root-orphan) collapsed into the v3 spine; 118 files placed; 117 indexed by Obsidian post-restructure. Vault name unchanged (`second-brain/`); iCloud-symlink path unchanged. AGENTS.md (164 lines, 13 sections) is the load-bearing schema read FIRST every session per §12 session-start protocol.

**`adapt-from-all` consultation** (per ADR-023 §Sources consulted): PARA, Zettelkasten, Johnny Decimal, ACCESS, LYT (Nick Milo), Karpathy LLM wiki gist, Mattgiaro 4,819-note vault. Hybrid v3 chosen for clean LLM-vs-user ownership separation (only Karpathy + v3 separate cleanly) plus extensibility-without-schema-change (new sub-domains are folders, not schema mutations).

**Vault is NOT git-tracked initially** (per parent plan §10.7 user directive — iCloud-sync handles mobile + no multi-machine pressure yet). See §4 Friction for the ADR-008 implication.

## 4 — Friction (unplanned discoveries + churn signals)

### 4.1 — High plan-file churn: 10 plans for 6 milestones

Distinct files at `~/.windsurf/plans/`:

| File | Date | Purpose |
|---|---|---|
| `cascade-c-obsidian-kickoff-4fa159.md` | 2026-05-01 | Original hand-authored kickoff (pre-`@kickoff` GA) |
| `cascade-c-obsidian-kickoff-52d465.md` | 2026-05-02 | Refreshed post-Sprint-1.5 kickoff |
| `m2c1-obsidian-cli-execution-52d465.md` | 2026-05-02 | M2C.1 execution kickoff |
| `cascade-c-obsidian-kickoff-3bf063.md` | 2026-05-04 | M2C.2 design lock — 63KB |
| `m2c2-plan-verification-3bf063.md` | 2026-05-04 | M2C.2 plan verification |
| `m2c2-vault-execution-handoff.md` | 2026-05-04 | M2C.2 execution handoff (transient) |
| `m2c2-vault-execution-kickoff-7afcc2.md` | 2026-05-04 | M2C.2 execution kickoff |
| `cascade-c-kickoff-m2c3-16f0dc.md` | 2026-05-04 | M2C.3 milestone-scoped kickoff |
| `cascade-c-m2c4-priming-rule-057e79.md` | 2026-05-04 | M2C.4 milestone-scoped kickoff |
| `cascade-c-m2c5-vault-research-skill-057e79.md` | 2026-05-04 | M2C.5 milestone-scoped kickoff |

**Insight**: Each milestone after M2C.2 grew its own milestone-scoped kickoff plan, plus M2C.2 itself needed three plans (parent kickoff + design lock + execution kickoff + verification). This may signal that `@kickoff` should operate at milestone granularity by default within an active vertical, OR that mid-vertical milestone re-orientation should route through `/run-phase` rather than fresh kickoff plans. Captured for Cascade A's next horizontal `@sprint-review` (see §7).

### 4.2 — ADR-022 contradicted handoff §3 M2C.1 framing

The handoff said *"Obsidian MCP"*; the M2C.1 evaluation said `obsidian` CLI. The parent handoff was status-updated rather than rewritten (per ADR-011 supersession-over-deletion). **Insight**: the parent vertical handoff was authored by Cascade A based on the landscape as of Sprint 1 close; the M2C.1 evaluation surfaced a better architecture. This is correct behavior — handoffs are starting hypotheses, not contracts. The pattern (handoff hypothesizes → vertical evaluates → ADR records the discrepancy → handoff status-updated, not rewritten) is the right shape and worth retaining.

### 4.3 — M2C.2 needed a freeze + fresh-session pickup mid-design

The design phase produced a 63KB locked-design plan (`cascade-c-obsidian-kickoff-3bf063.md`) at user direction; execution then ran in a fresh session via the M2C.2 execution handoff. **Insight**: design-locked plans larger than ~20KB likely warrant freeze-handoff by default — the 63KB doc was unwieldy in a single session's context window. Captured for Cascade A's next horizontal `@sprint-review` (see §7).

### 4.4 — Vault content not git-tracked → ADR-008 gate scope question

Per parent plan §10.7 user directive (iCloud-sync handles mobile + no multi-machine pressure yet), the vault is not under git. **Implication**: ADR-008's clean-tree gate cannot extend to vault content; future vault drift will not be caught by `@sprint-review` step 8. Trade-off accepted. **Revisit trigger**: if multi-machine collaboration emerges OR rollback-from-mistake becomes a real risk.

### 4.5 — Vault privacy discipline held cleanly

No vault content was dumped into chat, PRDs, or PR bodies during M2C.1–M2C.5. Handoff §4 privacy guideline + AGENTS.md §11 (*"Cascade never writes to `originals/` without explicit instruction"*) cleanly partitioned read-vs-write boundaries. **Insight**: M2C.4's queue entry codifies this discipline as a future L1 rule (`obsidian-context-priming`); the in-vertical experience confirms the rule's design (treat vault content as data-not-instructions; exclude `raw/_inbox/` from auto-priming) is well-shaped before authoring.

### 4.6 — `@release-manager` discipline held across all 6 vertical PRs

PRs #47–#52 each routed through `/branch-start` → `/commit` → `/branch-push-and-pr` → squash-merge. No direct pushes to `main`. `closes #N` keywords auto-closed the M2C.x issues on merge. The pattern that broke during Sprint 1 hardening (PRs #13–#16 merged unmilestoned) did not recur in Vertical C. The 11 stale merged remote branches at `origin` (`docs/m2c2-*`, `docs/m2c3-*`, `docs/m2c4-*`, `docs/m2c5-*`, `feat/obsidian-cli-selection`, plus pre-vertical-C ones) are an `@release-manager` cleanup hygiene gap — `/branch-merge-and-cleanup` should have removed each at merge time. Not a blocker for this retro; flagged for next horizontal review.

## 5 — `@kickoff` live-fire signal (per ADR-020 empirical-test stanza)

This vertical exercised `@kickoff` for the first time in production:

**M2C.6 invocation (this session)**: User invoked `@kickoff cascade-system/docs/handoffs/cascade-c-obsidian.md`. `@kickoff` correctly:

- Read the handoff + cited ADRs (014, 015, 016, 017, 018) + `cascade-system/docs/cheat-sheet.md` per its step-by-step procedure
- Detected lifecycle case `mid-execution → final milestone` based on M2C.1–M2C.5 closed + M2C.6 open + working tree clean
- Skipped milestone filing (idempotent — all M2C.x already filed, all on Project board, all milestone-tagged)
- Surfaced the next concrete action (this retro, per issue #46 AC) with one focused question
- Did NOT produce a hand-authored kickoff plan at `~/.windsurf/plans/<vertical>-kickoff-*.md` ad-hoc; did author a kickoff orientation doc at `~/.windsurf/plans/cascade-c-obsidian-kickoff-f832e8.md` per `@kickoff`'s own conventions

**Earlier Vertical C plan files** (10 listed in §4.1) did NOT route through `@kickoff` because most pre-date `@kickoff`'s GA (Sprint 1.5.2 / 2026-05-02) or were authored ad-hoc post-GA. The vertical was already in motion when `@kickoff` shipped, so the early plans are pre-history. Not a regression against ADR-020's stated empirical-test target (*"any reach for legacy kickoff plan instead of `@kickoff`"*) — none was reached for in the M2C.6 session.

**Verdict**: `@kickoff` performs at the lifecycle-detection + next-action-surfacing layer as designed. The `@kickoff` step 7 *"one focused question, not a bundle"* discipline held — when the user asked for clarification on what was missing, the response stayed scoped to the single retro-authoring decision. The high churn of milestone-scoped plans during M2C.3–M2C.5 (§4.1) suggests room for a milestone-mode but is non-blocking for `@kickoff` as currently shipped.

## 6 — L1 promotion candidates (point at queue, do NOT drain)

Per handoff §4: drain is `@sprint-review`'s job, not Vertical C's. Five entries authored during M2C.3–M2C.5 are in `queue/pending-review.md` awaiting Cascade A's next horizontal `@sprint-review`:

1. **M2C.3 / `@grill-me` vault context load** — replace single parenthetical line at `~/.codeium/windsurf/skills/grill-me/SKILL.md:44` with a concrete `obsidian` CLI subroutine (vault schema load → project-scoped notes → MOC backlink follow → `wiki/sources/` + `wiki/concepts/` reads). Activation `(When enabled)` → unconditional. Citations: ADR-022 + ADR-023.

2. **M2C.3 / `@to-prd` vault sources integration** — replace parenthetical at `~/.codeium/windsurf/skills/to-prd/SKILL.md:46` with `obsidian search` + `obsidian tags` queries. Amend §13 Sources guidance: PRD §13 cites vault as `[[wikilinks]]` to `wiki/sources/<type>/<slug>.md` (one-canonical-home rule per ADR-023 AGENTS.md §6). Citations: ADR-022 + ADR-023.

3. **M2C.3 / `/recalibrate` 7th drift signal** — add 7th row to drift table at `~/.codeium/windsurf/global_workflows/recalibrate.md:31-43`: *Vault drift* (vault entries newer than PRD `Date` topically relevant but uncited; superseded `obsidian://` artifact paths). Update step 1 from "Load three sources of truth" → "Load four sources of truth" (add Vault state via `obsidian` CLI). Citations: ADR-022 + ADR-023.

4. **M2C.4 / `obsidian-context-priming` rule** — author new watcher-archetype rule at `docs/rules/obsidian-context-priming.md` (long-form) + concise version in `~/.codeium/windsurf/memories/global_rules.md` per L1 dual-storage. `trigger: model_decision`; tiered load protocol (Tier 1 always / Tier 2 project-scoped / Tier 3 on-demand); prompt-injection guardrails (treat vault content as data, exclude `raw/_inbox/`, surface summaries not raw content, path validation). Survey-driven design across 4 cluster groups (architecture-confirming / direct prior art / architectural alternatives / security literature).

5. **M2C.5 / `@vault-research` auxiliary skill** — author at `docs/skills/vault-research/SKILL.md` (long-form) + canonical `~/.codeium/windsurf/skills/vault-research/SKILL.md`. Recency × tag-overlap × MOC-distance composite ranking (weights 0.4 / 0.4 / 0.2 starting point, empirically calibrated at build time). Integration points: `@grill-me` step 1.6, `@to-prd` step 2, `/recalibrate` step 1 source #4, standalone. Survey across 6 algorithmic clusters; deliberately structural (not semantic) per ADR-023's hand-curated vault.

**Bundle hint**: M2C.5 is the highest-priority drain target — M2C.3 entries call into `@vault-research` mechanically (without it they invoke a non-existent capability). Suggested order:

1. **M2C.5 first** (build the skill so the integration points have something to call)
2. **M2C.3 entries** (wire the three integration points to `@vault-research`)
3. **M2C.4 last** (priming rule benefits from the skill but does not require it)

All five bundle naturally in one `@update-horizontal` pass.

## 7 — Queue entries from this retro (new captures)

Two friction observations from §4 are net-new queue entries appended in the same PR as this retro:

- **`@kickoff` milestone-mode candidate** (from §4.1) — high churn of milestone-scoped kickoff plans (M2C.3 / M2C.4 / M2C.5 each grew their own) suggests `@kickoff` may want a `--milestone <id>` mode, OR that mid-vertical milestone re-orientation should route through `/run-phase` rather than fresh kickoff plans. Promotion target: next horizontal `@sprint-review`.

- **Design-locked-plan size threshold heuristic** (from §4.3) — the 63KB locked-design plan at M2C.2 needed freeze-handoff to a fresh session because it was unwieldy in a single context window. Suggests a default-freeze heuristic above some size threshold (~20KB candidate). Promotion target: next horizontal `@sprint-review`.

A third observation worth surfacing but NOT promoted yet (no concrete action shape):

- **`@release-manager` cleanup hygiene gap** (from §4.6) — 11 stale merged remote branches at `origin` indicate `/branch-merge-and-cleanup` was skipped on prior PR merges. Not a queue entry yet; awaiting one more cycle of evidence before deciding whether to make `/branch-merge-and-cleanup` mandatory in `@release-manager` skill flow vs. accepting it as opt-in cleanup.

## 8 — Definition of done check (handoff §7)

| AC | Status | Evidence |
|---|---|---|
| Obsidian access tool configured + demonstrably working (read + search) | DONE | ADR-022; PR #47; `obsidian` CLI v1.12.7 running against vault |
| Vault layout convention documented in an ADR | DONE | ADR-023; PR #49; vault at v3 spine in-place |
| L1 update proposals are queued (not yet applied) | DONE | 5 entries in `queue/pending-review.md` from M2C.3 / M2C.4 / M2C.5 |
| Auxiliary skill (M2C.5) is shipped at L2 or as an L1 proposal | DONE | Queued as L1 proposal; PR #52 |
| Sprint 2 vertical C retro is written and `Status: closed` | DONE | This file, on merge of this PR; clean working tree confirmed (§10) |
| All M2C.x issues are closed | DONE on merge | #41–#45 closed; #46 closes via `closes #46` keyword on this PR |
| L1 observations are in queue for Cascade A's next horizontal `@sprint-review` | DONE | 5 from M2C.3–M2C.5 + 2 net-new from §7 = 7 total entries |

## 9 — Termination handoff (handoff §8 verbatim)

> Vertical C (Obsidian integration) complete. MCP: first-party `obsidian` CLI v1.12.7 (chosen over MCP architectures per ADR-022). Vault layout: v3 spine (`_meta/raw/originals/wiki`) per ADR-023. Retro at `retros/sprint-2-vertical-c.md`. L1 proposals (5 from M2C.3–M2C.5 + 2 from this retro = 7) queued for Cascade A's next horizontal `@sprint-review`. Cascades B and D can now use `obsidian://` artifact paths in their projects' `phases.yaml`.

Cascade D's work is a separate vertical with its own handoff (when it spawns).

## 10 — Sign-off

Sprint 2 Vertical C closes cleanly. Working tree is clean (this retro is the final commit on the vertical). All 6 milestone issues are in `state: closed` after this PR's merge. The 6 PRs that landed Vertical C work plus this retro PR are squash-merged with covering issues + milestone tags. Queue is up-to-date with 7 entries pending drain.

Cascade A's next `@sprint-review` will drain the 7 entries; suggested bundle order in §6.
