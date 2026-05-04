# ADR-025: `@grill-me` step 1.6 vault-context integration

**Status**: Accepted
**Date**: 2026-05-04
**Plan**: `~/.windsurf/plans/sprint-2-vertical-c-drain-86a684.md` Phase 2.2
**Source retro**: `~/Projects/cascade-system/retros/sprint-2-vertical-c-drain.md` §3 (row 7) + §4 (Phase 2.2)
**Source queue entry**: M2C.3 (`Sprint 2 — cascade-system — Cascade C — M2C.3 (`@grill-me` vault context load)`)
**Related**: ADR-022 (Obsidian CLI), ADR-023 (vault layout v3), ADR-024 (`@vault-research` skill — delegation target)
**Issue**: [#43](https://github.com/ReebalSami/cascade-system/issues/43) (M2C.3)

## Context

`@grill-me` step 1 "Load context" previously carried a single parenthetical line at `~/.codeium/windsurf/skills/grill-me/SKILL.md:44` (pre-edit):

```
- (When enabled) Obsidian: query linked cross-project notes
```

The touchpoint predated M2C.1 (ADR-022 — Obsidian CLI adoption) and M2C.2 (ADR-023 — vault layout v3). In its pre-ADR-025 shape:

- No concrete query surface specified (implicit mechanism)
- No vault-resident routing (pre-ADR-023 the vault had no deterministic spine)
- Didn't honor `_meta/AGENTS.md` §12 session-start protocol (pre-ADR-023 no AGENTS.md existed)
- `(When enabled)` gate was an artifact of the pre-M2C.1 era when vault access was an optional future capability

Post-ADR-022 + ADR-023 + ADR-024, all three prerequisites are met:

- CLI primitives exist (`obsidian search`, `obsidian read`, `obsidian backlinks`, `obsidian tags`)
- Vault has a deterministic spine (`_meta/`, `originals/`, `raw/`, `wiki/`) that makes the read concrete
- Topic-scoped research has a skill to delegate to (`@vault-research`)

## Decision

Replace the single parenthetical line with a concrete 3-substep subroutine, and update frontmatter `sources_consulted` + `adapted_for`.

**New step 1.6** (replacing line 44):

```
- Obsidian vault (if CLI available per ADR-022; graceful-skip otherwise):
    a. Session-start schema load per _meta/AGENTS.md §12:
       obsidian read file=_meta/AGENTS.md
       → obsidian read file=_meta/log.md (tail 10 entries)
       → obsidian read "file=wiki/mocs/MOC - home.md"
       Once obsidian-context-priming rule lands (Phase 2.5), this step fires
       ambiently session-start; until then, fire it here.
    b. Project-canonical-notes discovery for software projects:
       resolve notes in originals/software/projects/<repo>/ via
       obsidian search or frontmatter linked_software:<repo> scan.
       Read with intent — targeted queries, never dump unrelated notes.
    c. Topic-scoped research — invoke @vault-research <grill-topic> (ADR-024)
       for a ranked, reasoned shortlist of vault entries relevant to the
       interview topic. Delegation keeps the ranking algorithm in one place.
```

**Activation**: changed from `(When enabled)` → unconditional with `if CLI available` graceful-skip guard. Rationale: per ADR-022 §"Constrains" §1, the Obsidian app is always-on during the user's knowledge work; the CLI is the sole runtime dependency. Treating vault access as "optional / future" (the `(When enabled)` gate) is no longer accurate.

**Frontmatter updates**:

- `sources_consulted`: added ADR-022, ADR-023, ADR-024 rows
- `adapted_for`: added bullet for the ADR-025 edit shape

## Why three substeps (1.6a/b/c) rather than one bullet

The queue entry proposed inlining all 4 substeps (schema load + project notes + MOC backlink walk + `wiki/sources/` read). After Phase 2.1 landed `@vault-research`, substeps 3+4 (MOC backlink walk + `wiki/sources/` read) are redundant with `@vault-research`'s ranking algorithm. Keeping them in `@grill-me` would duplicate logic across three M2C.3 integration targets.

Architectural cleanup:

- **1.6a (schema load)**: stays inline — session-start protocol, not topic-scoped
- **1.6b (project-canonical notes)**: stays inline — project-scoped ambient, not topic-scoped
- **1.6c (topic research)**: delegates to `@vault-research` — topic-scoped, one-skill-one-purpose

This delegation pattern is adopted by ADR-026 (`@to-prd`) and ADR-027 (`/recalibrate`) as well — single source of truth for ranking.

## Why unconditional activation (not gated)

The pre-ADR-025 `(When enabled)` framing implied a future toggle (*"enable vault access via config"*). That toggle never existed and ADR-022 made it unnecessary — the CLI availability check is runtime, not config-time. `if CLI available` is discovery + graceful-skip (per ADR-022 line 83: hang → surface *"relaunch Obsidian"* + exit 142), not a user-configurable flag.

## Alternatives considered

| Candidate | Shape | Verdict |
|---|---|---|
| **Inline all 4 substeps** (queue-entry literal) | Schema load + project notes + MOC walk + `wiki/sources/` read all in `@grill-me` | Rejected — substeps 3+4 duplicate `@vault-research`'s ranking; three skills each reinvent |
| **Delegate everything to `@vault-research`** | Step 1.6 = single `@vault-research <project+topic>` call | Rejected — session-start schema load isn't topic-scoped; shoehorning it into `@vault-research` breaks that skill's single-purpose contract |
| **Keep `(When enabled)`** | Status quo | Rejected — gate is obsolete post-ADR-022 |
| **Defer to Phase 2.5 priming rule only** | Let priming rule cover everything ambient; `@grill-me` gets no explicit step | Rejected — priming rule hasn't landed yet (Phase 2.5); between Phase 2.2 and Phase 2.5 `@grill-me` would silently skip vault context. 1.6a note ("until priming rule lands, fire it here") bridges the gap |
| **Three substeps with delegation at 1.6c (chosen)** | Schema + project notes inline; topic research via `@vault-research` | Adopted — matches the architectural trio from M2C.3+M2C.4+M2C.5 queue entries |

## Consequences

**Enables**:

- `@grill-me` invocations in vault-mounted projects surface topically-relevant vault context at interview open (prevents re-plowing known ground)
- Ranking algorithm lives in one place (`@vault-research`) — one bug-fix becomes one edit, not three
- Privacy discipline preserved (read with intent, `raw/_inbox/` excluded per `@vault-research` guardrails inherited from M2C.4 cluster)
- Graceful-skip when CLI unavailable — `@grill-me` doesn't fail hard in non-vault projects or when Obsidian is closed

**Constrains**:

- Step 1.6a creates duplication with the forthcoming `obsidian-context-priming` rule (Phase 2.5). The self-aware note in 1.6a (*"until priming rule lands, fire it here"*) is the temporary bridge; Phase 2.5 ADR (ADR-028) will remove the 1.6a substep from `@grill-me` in favor of ambient priming
- Non-vault projects still run step 1.6 graceful-skip — the extra conditional adds ~2 LOC of skill text; negligible cognitive load
- `@vault-research` must be live (Phase 2.1 / ADR-024) — delegation target requirement. Phase 2.1 shipped first for this reason (bundle-leader sequencing)

**Deferred**:

- ADR-028 (Phase 2.5 priming rule) will remove step 1.6a from this skill since priming will fire ambiently. This ADR deliberately ships with the temporary duplication to avoid Phase 2.2 blocking on Phase 2.5
- Empirical calibration of how often `@vault-research` returns below-threshold no-result — capture per-vertical evidence at next sprint review

## Sources consulted

- cascade-system/queue/pending-review.md M2C.3 `@grill-me` entry (pre-deletion) — source spec + substep candidate list
- cascade-system/docs/decisions/ADR-022-obsidian-cli-selection.md (own) — CLI primitive selection + graceful-skip pattern
- cascade-system/docs/decisions/ADR-023-vault-layout-v3.md (own) — vault spine + AGENTS.md §12 + MOC convention
- cascade-system/docs/decisions/ADR-024-vault-research-skill.md (own) — delegation target for topic-scoped research
- cascade-system/docs/handoffs/cascade-c-obsidian.md §4 privacy guideline — read-with-intent discipline preserved
- `~/.codeium/windsurf/skills/grill-me/SKILL.md` (own, pre-edit) — line 44 touchpoint being replaced
- `~/.codeium/windsurf/skills/vault-research/SKILL.md` (own, Phase 2.1 output) — delegation target's interface

## Related ADRs

- ADR-006 (SKILL.md frontmatter schema) — applied
- ADR-009 (NNN reserve-in-INDEX-first) — applied
- ADR-014 (L1 canonical paths) — applied; skill lives at `~/.codeium/windsurf/skills/grill-me/SKILL.md`
- ADR-022, ADR-023, ADR-024 — see frontmatter updates
- ADR-028 (Phase 2.5, forthcoming) — will remove step 1.6a duplication when priming rule lands

## Provenance

Authored 2026-05-04 by Cascade A as Phase 2.2 of the Sprint 2 Vertical C drain. Promoted from M2C.3 `@grill-me` queue entry (issue [#43](https://github.com/ReebalSami/cascade-system/issues/43)) per `retros/sprint-2-vertical-c-drain.md` §3 row 7.
