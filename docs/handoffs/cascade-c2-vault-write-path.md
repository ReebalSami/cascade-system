# Handoff: Cascade C2 — vault write path (Sprint 3)

**From**: Cascade A (horizontal, Sprint 2 Vertical C drain closer)
**To**: Cascade C2 (vertical, owns the vault **write path** deferred from Vertical C)
**Sprint**: 3 (Vertical C2 — continues Vertical C's vault-integration thread)
**Parent plan**: `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` (strategic gap analysis authored during Vertical C close)
**Bookends**: `@vault-research` (ADR-024) is the **READ** half; C2 builds the **WRITE** half — same vault, opposite direction

You are a fresh Cascade picking up the write path that Vertical C intentionally left out of scope. Vertical C closed cleanly on 2026-05-04 (PRs #47–#53 + drain PRs #54–#61). This handoff is **self-contained** — disk + GitHub state + the user's Obsidian vault are your only inputs.

## 1 — Summary

Cascade C2 builds the vault **write path** (raw → `wiki/` distillation) deferred from Sprint 2 Vertical C. Vertical C shipped the READ path (layout per ADR-023, access tool per ADR-022, read-side L1 drained in PRs #55–#60) but left three holes, captured as queue entries GA-1 / GA-2 / GA-3 at `queue/pending-review.md:76–110`. C2 resolves **GA-1** (write path mechanism) and **GA-2** (ADR↔vault posture — ADR-030). **GA-3** stays deferred (blocked by Windsurf platform — no session-boundary callbacks).

C2 scope — in one paragraph:

- Decide an architecture among 3 candidates via `@grill-me` (see §5 Q1): user-invoked skill (`@vault-distill`), scheduled flush (Karpathy/Cole Medin — partially blocked by GA-3), or conversation-triggered.
- Decide an ADR↔vault posture among 3 (see §5 Q2): separation (current default) / unified discovery via `wiki/sources/adrs/` cards / vault-first authoring.
- Ship **ADR-030** (posture) + **chosen-architecture skill** (likely `@vault-distill` at `~/.codeium/windsurf/skills/vault-distill/` if Q1 resolves to Option 1) + a **first distillation run** that produces real cards in `wiki/`.
- Reference architecture: Karpathy's LLM-knowledge-base compiler pattern (see §6).

## 2 — Parent plan reference

`~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` — authored 2026-05-04 during Vertical C close. Three sections are load-bearing for C2:

- **§Q5** — three plausible architectures for the write path (maps to §5 Q1 here)
- **§Q6** — three postures for the ADR↔vault relationship (maps to §5 Q2 here)
- **§Q7** — Karpathy vs Cole Medin scope disambiguation (Karpathy pattern = external knowledge ingestion; Cole Medin's session-hook automation does NOT port to Windsurf — see §6)

The gap-analysis also catalogued three queue entries (GA-1 / GA-2 / GA-3) at `queue/pending-review.md:76–110`. Read them verbatim — they are the source of truth for C2's scope; this handoff is the execution framing.

## 3 — Cited ADRs to read

Read in ADR-NNN order. The `@kickoff` skill will pre-load these.

| ADR | Title | Why for C2 |
|---|---|---|
| 009 | NNN reserve-in-INDEX-first | Reserve ADR-030 in `docs/decisions/INDEX.md` BEFORE authoring M_C2.2 |
| 011 | Supersession over deletion | Any card C2 supersedes in `wiki/` keeps a back-pointer, not a delete |
| 014 | L1 canonical storage paths | Your skill lives at `~/.codeium/windsurf/skills/<name>/SKILL.md`, NOT `~/.windsurf/` |
| 015 | Skill vs workflow invocation syntax | `@vault-distill` (or whatever Q1 names) is a skill → `@`-invoked |
| 016 | Workflow canonical path correction | If C2 decides it needs a workflow helper, place at `~/.codeium/windsurf/global_workflows/` |
| 017 | `@propose-extension` intake channel | Your new skill routes through `@propose-extension` → `@write-skill` |
| 018 | Release-discipline cluster | `@release-manager` is mandatory for every `main`-bound commit in cascade-system |
| 022 | Obsidian CLI selection | First-party `obsidian` CLI v1.12.7+ is the write channel (not MCP; reasons in ADR-022) |
| 023 | Vault layout v3 | `wiki/` ownership (Cascade writes) + AGENTS.md §9 quality gate (Karpathy's "decline low-signal" rule is load-bearing for every distillation) |
| 024 | `@vault-research` skill | The READ bookend C2 pairs with; do NOT re-solve what ADR-024 already solved |
| 026 | `@to-prd` vault sources integration | §13 wikilink discipline + vault-source citation pattern |
| 028 | `obsidian-context-priming` rule | Ambient session primer is already active; C2 must not re-solve priming |
| 029 | `@verify-l1` path-drift sweep | Your skill MUST pass `@verify-l1` before PR merge |

## 4 — Milestones (file these as GitHub issues)

`@kickoff` step 3 files these on pickup. AC is shape-driven per `no-quantity-over-shape`.

- **M_C2.1 — Decision sprint**. Run `@grill-me` against Q1–Q4 in §5 below; produce a PRD via `@to-prd`; the PRD cites Karpathy clipping via `[[wikilink]]` per ADR-026 §13.
  - **AC**: PRD accepted + Q1 (architecture) and Q2 (posture) resolved in writing; Q3 (input scope) + Q4 (write discipline) resolved enough to execute M_C2.3.

- **M_C2.2 — ADR-030 ADR↔vault posture**. Reserve ADR-030 slot in `docs/decisions/INDEX.md` FIRST (per ADR-009); then author. Posture A / B / C per §5 Q2.
  - **AC**: ADR-030 `Status: Accepted`; INDEX row filled; ADR-023 §Deferred "ADR ↔ vault relationship" item closed (back-link from ADR-030).

- **M_C2.3 — Chosen-architecture skill**. Most likely `@vault-distill` SKILL.md at `~/.codeium/windsurf/skills/vault-distill/` (if Q1 = Option 1). Routes through `@propose-extension` → `@write-skill` per ADR-017.
  - **AC**: SKILL.md passes `@verify-l1`; frontmatter `sources_consulted` + `adapted_for` filled per `adapt-from-all`; outside-repo edit acknowledged in PR description per ADR-008 M-2 trade-off.

- **M_C2.4 — First distillation run**. Dogfood M_C2.3's skill against a real corpus (see §6 for candidate corpus).
  - **AC**: `wiki/` has at least one concept card AND one source card for the chosen sub-domain; `obsidian files path=wiki/` shows non-empty output; every card passes ADR-023 AGENTS.md §9 quality gate; frontmatter conforms to AGENTS.md §5.

- **M_C2.5 — Retro**. `retros/sprint-3-c2-write-path.md`; `Status: closed`; `@sprint-review-prompt` rule fires for horizontal A.
  - **AC**: retro closed; GA-1 + GA-2 queue entries REMOVED from `queue/pending-review.md` as RESOLVED; GA-3 stays (platform-blocked).

## 5 — Open design questions (grill in M_C2.1)

Four questions. Pre-recommendations inherited from the gap analysis — do NOT treat them as pre-solved; `@grill-me` surfaces the full design tree.

### Q1 — Architecture

Three candidates (per gap analysis §Q5):

| Option | Shape | Verdict from gap analysis |
|---|---|---|
| **1** | `@vault-distill` skill — user-invoked (*"Cascade, distill the N clippings in `raw/_inbox/<topic>/` into `wiki/concepts/<domain>/`"*) | Weakly recommended for v1 — cheapest, auditable, low-risk, bookends `@vault-research` |
| **2** | Scheduled flush (cron / session hook) — Karpathy + Cole Medin pattern | Blocked-ish: Windsurf lacks session hooks (GA-3); external cron is possible but heavier infrastructure |
| **3** | Conversation-triggered (at end of `@grill-me` / `@to-prd` / etc., ask *"Should findings X, Y, Z be written to `wiki/concepts/<topic>`?"*) | Most intellectually honest ("write-only-what-you-learned"); weak recommendation as v2 evolution of Option 1 |

Don't pre-commit. `@grill-me` may surface a hybrid.

### Q2 — ADR↔vault posture

Three postures (per gap analysis §Q6):

| Posture | Shape | Verdict from gap analysis |
|---|---|---|
| **A** | Separation — cascade-system `docs/decisions/` stays canonical; vault never sees ADRs | Current default; acceptable until `wiki/` has content |
| **B** | Unified discovery — ADR files stay at `docs/decisions/`; vault adds `wiki/sources/adrs/<slug>.md` cards with 1-line summary + `[[wikilinks]]` + path back to canonical | **Strongly recommended** per gap analysis §Q6 — preserves one-canonical-home rule while giving the vault reachability into ADRs |
| **C** | Vault-first — ADRs authored inside vault, rendered/exported to `docs/decisions/` for git history | Over-engineered for current size |

### Q3 — Distillation input scope

`raw/_inbox/` only, or also `originals/`? No pre-recommendation; trade-off is about whether the distiller ever touches user-authored content (`originals/` ownership = Reebal per ADR-023). If Q3 resolves to "`raw/_inbox/` only", that constrains the skill signature in M_C2.3.

### Q4 — Write discipline

Auto-commit cards to vault (iCloud syncs them) OR dry-run + user approval gate? No pre-recommendation. Q4 determines whether the skill has a `--dry-run` flag that's the default or an opt-in.

## 6 — Reference architecture

**Karpathy LLM-knowledge-base compiler pattern** — the closest prior art for C2's write path. Explicitly cited by the user as the reference.

**Canonical clipping (user-named)**:

```
~/Projects/obsidian/second-brain/raw/clippings/2026-05-04_i-built-self-evolving-claude-code-memory-w-karpathys-llm-knowledge-bases.md
```

**Candidate first-distillation corpus for M_C2.4** — four on-disk Karpathy clippings already in `raw/clippings/`:

1. `2026-05-04_i-built-self-evolving-claude-code-memory-w-karpathys-llm-knowledge-bases.md` (canonical clipping above)
2. `2026-05-04_i-used-karpathy's-autoresearch-to-train-an-llm.md`
3. `2026-05-04_karpathys-obsidian-rag-plus-claude-code-equal-cheat-code.md`
4. `2026-05-04_karpathys-wiki-vs-open-brain-one-fails-when-you-need-it-most.md`

Framed as a **candidate target, not a mandate**. Q3 (§5 above) stays open — if `@grill-me` surfaces a different corpus (e.g., thesis-related sources C2 finds in `raw/papers/`), that's fine. Distilling the Karpathy clippings has two convenient side-effects: (a) clean self-reference dogfood — the first write-path run distills write-path literature; (b) closes the ADR-023 §Consequences deferred item *"Clipping ingestion to `wiki/sources/articles/`"*.

**Why Karpathy maps to Option 1 / 3 (not Option 2)**:

- Karpathy's `raw → LLM → wiki` compiler is **user-invoked** per clipping (*"when you read something interesting, run the compiler against it"*) — directly maps to Option 1 (`@vault-distill` as a user-invoked skill).
- Karpathy also hints at **conversation-triggered** flushes (*"when a conversation produces a new finding, compile it into the wiki"*) — maps to Option 3.
- Karpathy does NOT require scheduled flushes — Option 2's session-hook automation is Cole Medin's Claude-Code-specific addition, and Windsurf platform doesn't support it (per GA-3 + gap analysis §Q7).

## 7 — Read-list (orientation order)

Read top-to-bottom when picking up via `@kickoff`.

| # | Path | Why |
|---|---|---|
| 1 | `cascade-system/AGENTS.md` | Auto-loaded on workspace open; start-here orientation |
| 2 | `cascade-system/docs/cheat-sheet.md` | Scannable single-page inventory of the whole system |
| 3 | `cascade-system/docs/manual.md` | Invocation cheat-sheet + mental model + release discipline |
| 4 | `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` | Parent plan — §Q5 / §Q6 / §Q7 load-bearing |
| 5 | `cascade-system/retros/sprint-2-vertical-c-drain.md` | §3 GA-1/GA-2/GA-3 rows + §9 "what this drain did NOT change" names the write-path gap |
| 6 | `cascade-system/queue/pending-review.md` lines 76–110 | GA-1/GA-2/GA-3 captured entries — verbatim scope source |
| 7 | The 13 cited ADRs in §3 above | Load-bearing ADRs; read in order |
| 8 | `~/.codeium/windsurf/skills/vault-research/SKILL.md` | The READ bookend; study its shape before authoring the WRITE skill |
| 9 | `~/.codeium/windsurf/memories/global_rules.md` | Active L1 rule surface (includes `obsidian-context-priming`) |
| 10 | `~/Projects/obsidian/second-brain/_meta/AGENTS.md` | Vault schema — 13 sections; §9 quality gate is load-bearing |
| 11 | The 4 Karpathy clippings at §6 | Read the canonical one first; scan others if Q3 resolves to ingest them |
| 12 | `cascade-system/docs/handoffs/cascade-c-obsidian.md` | Predecessor handoff — shape exemplar + Vertical C context |
| 13 | `cascade-system/docs/decisions/INDEX.md` | Reserve ADR-030 slot here BEFORE authoring M_C2.2 |

## 8 — Operating constraints

All L1 rules in `~/.codeium/windsurf/memories/global_rules.md` apply. C2-specific highlights:

- **`@propose-extension` for all L1 proposals** (ADR-017) — your new skill routes through it; it dispatches to `@write-skill`.
- **`@release-manager` mandatory for every `main`-bound commit** (ADR-018) — use `/branch-start <type>/c2-<slug>` per unit of work; never `git push origin main` directly.
- **ADR-009 NNN reserve-in-INDEX-first** — reserve ADR-030 before writing the ADR body.
- **`adapt-from-all`** — research write-path / distiller prior art: `refs/superpowers/`, `refs/mattpocock-skills/`, `refs/awesome-agent-skills/`, `refs/claude-skills/`, plus the 4 Karpathy clippings + llmwiki-cli (`https://github.com/doum1004/llmwiki-cli`, already cited in ADR-023). Cite `sources_consulted` in skill frontmatter.
- **ADR-023 AGENTS.md §9 quality gate** — Karpathy's "decline low-signal / duplicate / untrustworthy / out-of-scope content" rule applies to EVERY card the distiller writes. The skill must include the gate as a hard step, not an optional hint.
- **ADR-011 supersession over deletion** — if the distiller wants to replace an existing `wiki/` card, it supersedes with a back-pointer; never silent-overwrite.
- **Bundle anti-pattern** (per `@update-horizontal` SKILL.md) — one ADR per logical change. ADR-030 (posture) and the skill's SKILL.md author in SEPARATE PRs. Don't bundle.
- **Privacy** — vault contains personal notes; `originals/` especially. If Q3 resolves to include `originals/`, the distiller MUST surface only what's directly relevant to the active project and MUST NOT dump unrelated notes.
- **Outside-repo edits** — the skill writes to `~/.codeium/windsurf/skills/<name>/` (L1 surface; unversioned per deferred M-2 decision). Every PR description explicitly acknowledges the outside-repo edit per ADR-008 clean-tree gate's M-2 trade-off.
- **`@verify-l1` gate** — every skill-authoring PR runs `@verify-l1` before merge; ADR-029 expanded the sweep to catch path drift + workspace-only-rule awareness.
- **`no-time-estimates` / `no-quantity-over-shape` / `no-terminal-oneline-scripts` / `make-sure-it-works` / `be-honest-direct-critical`** — all apply.
- **`bidirectional-learning-pipe`** — capture cross-project insights during C2 into `queue/pending-review.md` for Cascade A's next `@sprint-review`.

ADR routing per ADR-001: issue close + comment via MCP (`mcp3_*`); repo / Project v2 mutations via `gh` CLI with `GITHUB_TOKEN= GH_TOKEN=` prefix.

## 9 — Definition of done

You're finished when ALL of the following hold:

- [ ] ADR-030 (ADR↔vault posture) accepted; numbered via INDEX-reserve-first per ADR-009
- [ ] Chosen-architecture skill authored; passes `@verify-l1`; `sources_consulted` + `adapted_for` frontmatter filled
- [ ] First distillation run produces at least one concept card AND one source card in `wiki/` (verified via `obsidian files path=wiki/` + every card passes ADR-023 AGENTS.md §9 quality gate)
- [ ] `retros/sprint-3-c2-write-path.md` written and `Status: closed`
- [ ] `queue/pending-review.md` GA-1 + GA-2 entries REMOVED as RESOLVED; GA-3 remains (platform-blocked)
- [ ] All M_C2.x GitHub issues closed
- [ ] Clean working tree in `cascade-system/`; outside-repo edits acknowledged per PR descriptions
- [ ] **B (`python-ml-uv`) is unblocked only when C2's retro closes (`Status: closed`); B's handoff stays as-is — the ordering discipline lives in C2's DoD, not in cross-vertical edits.**

**About that last item** — horizontal Cascade A authored this handoff with an explicit ordering override: **C2 blocks B**. B (`python-ml-uv`) is an L3 template; templates bake patterns into every project bootstrapped from them. If B lands before C2, B's `phases.yaml` + scaffold won't include vault-write phases (distill → `wiki/`), and every ML project bootstrapped from B — including D (thesis) — will inherit that gap. Retrofitting templates after instances exist is painful. B staying stale a bit longer is the accepted trade-off so B's template shape bakes in vault-integration from day 1. No edit to B's handoff is expected in any C2 PR; the discipline lives HERE in C2's DoD only.

## 10 — Termination + handoff

When done, your last action is:

> Vertical C2 (vault write path) complete. Architecture: **<Option 1/2/3>**. ADR-030 posture: **<A/B/C>**. Skill at `~/.codeium/windsurf/skills/<name>/`. First distillation: **<N>** cards in `wiki/` covering **<sub-domain>**. Retro at `retros/sprint-3-c2-write-path.md`. GA-1 + GA-2 resolved; GA-3 remains platform-blocked. **Vertical B is now unblocked** — Cascade A can `@kickoff cascade-system/docs/handoffs/cascade-b-template-python-ml-uv.md` when bandwidth allows; B's scaffold + phases.yaml can now bake in vault-write phases.

Do **not** start Cascade B or D's work yourself. Each has its own handoff (B's exists at `docs/handoffs/cascade-b-template-python-ml-uv.md`; D's is authored when B closes).

## 11 — Provenance

Pattern derived from `docs/decisions/ADR-002-handoff-prompts-subdirectory.md`. Shape per `~/.windsurf/plans/cascade-c2-write-path-handoff-86a684.md` §"What the handoff will contain". Sister handoffs: `cascade-b-template-python-ml-uv.md` (sibling, blocked by this one) + `cascade-c-obsidian.md` (predecessor — READ path).

Authoring plan: `~/.windsurf/plans/cascade-c2-write-path-handoff-author-f86c5c.md`.
Gap analysis (parent): `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md`.
Vertical C drain retro: `retros/sprint-2-vertical-c-drain.md`.
