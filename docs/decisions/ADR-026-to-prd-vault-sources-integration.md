# ADR-026: `@to-prd` vault sources integration

**Status**: Accepted
**Date**: 2026-05-04
**Plan**: `~/.windsurf/plans/sprint-2-vertical-c-drain-86a684.md` Phase 2.3
**Source retro**: `~/Projects/cascade-system/retros/sprint-2-vertical-c-drain.md` §3 (row 8) + §4 (Phase 2.3)
**Source queue entry**: M2C.3 (`Sprint 2 — cascade-system — Cascade C — M2C.3 (`@to-prd` vault sources integration)`)
**Related**: ADR-022 (Obsidian CLI), ADR-023 (vault layout v3 — AGENTS.md §6 one-canonical-home rule), ADR-024 (`@vault-research` delegation target), ADR-025 (sibling `@grill-me` delegation pattern — adopts same shape)
**Issue**: [#43](https://github.com/ReebalSami/cascade-system/issues/43) (M2C.3)

## Context

`@to-prd` step 2 "Load context" previously ended with a single implicit bullet at `~/.codeium/windsurf/skills/to-prd/SKILL.md:46` (pre-edit):

```
- (If enabled) read linked Obsidian notes.
```

Same implicit-mechanism problem as `@grill-me` (ADR-025 parallel): no concrete CLI surface, no awareness of ADR-023 `wiki/sources/` + `wiki/concepts/` split, no routing guidance for PRD §13 Sources.

**Downstream problem**: handoff `docs/handoffs/cascade-c-obsidian.md` §3 M2C.3 bullet 2 explicitly requires *"incorporate vault findings into §13 Sources"*. Without a citation discipline, `@to-prd` has no way to emit PRD §13 entries that point to canonical vault locations — the `wiki/sources/<type>/<slug>.md` files ADR-023 established as the one-canonical-home for ingested research would get re-cited as external URLs, duplicating across PRDs and violating AGENTS.md §6.

## Decision

**Two coupled edits** (one skill, one template):

### Edit 1 — `@to-prd` SKILL.md step 2

Replace the single `(If enabled) read linked Obsidian notes` bullet with delegation to `@vault-research`:

```
- Vault research (if Obsidian CLI available per ADR-022; graceful-skip
  otherwise): invoke @vault-research <brainstorm-topic> (ADR-024) for a
  ranked, reasoned shortlist of vault entries relevant to the PRD topic.
  These candidates inform §13 Sources (see step 3 below for citation
  discipline). If the brainstorm cites specific MOCs or wiki concepts,
  pass those as sub-topics (one @vault-research call per major topic;
  bundle results into a single candidate list for §13).
```

### Edit 2 — `@to-prd` SKILL.md step 3 + PRD template §13

Append a **§13 Sources citation discipline** block to step 3 that codifies three rules:

1. **Vault-resident sources** cited as `[[wikilinks]]` to canonical `wiki/sources/<type>/<slug>.md` (or `wiki/concepts/<domain>/<sub>/<name>.md`) paths — never as file-path strings, never as external URLs. One canonical home per source (ADR-023 AGENTS.md §6).
2. **Per-source reasoning** — one sentence per citation explaining why this entry matters for the PRD (e.g., *"constrains §7 solution architecture"* or *"validates §1 problem framing with user-collected evidence"*). Without reasoning, §13 is a dump; with reasoning, §13 is auditable.
3. **External-web-only sources** (not ingested into vault) keep plain URL citations. If a source seems worth ingesting, flag as deferred-ingest candidate — don't block PRD drafting on it. If `@vault-research` returned a below-threshold no-result, §13 lists only external references (valid state, not a gap).

PRD template §13 gets an updated structure showing the wikilink format + external fallback, so every new PRD bootstraps with the right shape.

### Frontmatter updates

- `sources_consulted`: ADR-022 + ADR-023 + ADR-024 added
- `adapted_for`: new bullet naming the ADR-026 edit shape

## Why the delegation pattern (same as ADR-025)

Phase 2.2 (`@grill-me`) and Phase 2.3 (`@to-prd`) both replaced implicit Obsidian touchpoints with `@vault-research` delegation. Phase 2.4 (`/recalibrate`) will adopt the same. Three skills, one delegation target — ranking algorithm lives in one place.

Alternative (inlining CLI substeps in each skill) was rejected in ADR-024 + ADR-025 for exactly this reason. This ADR reinforces the pattern.

## Why citation discipline matters (the §13-is-load-bearing argument)

PRDs are the durable record of what the project committed to at the spec phase. §13 Sources is where the research trail lives. Without a citation discipline:

- Vault entries get re-cited as external URLs → one source now lives in two places (vault + PRD) → AGENTS.md §6 violated → knowledge graph drifts
- No per-source reasoning → §13 becomes a list of "stuff I read" rather than "evidence the PRD rests on" → auditability lost
- External and vault citations indistinguishable → reader can't tell which sources the user has deeply ingested vs. merely browsed

The discipline is three rules, not a framework. Low cost, high auditability return.

## Alternatives considered

| Candidate | Shape | Verdict |
|---|---|---|
| **Inline CLI substeps in `@to-prd`** | Step 2 enumerates `obsidian search` + `obsidian tags` + MOC walk inline | Rejected — duplicates ADR-024's ranking algorithm across three integration targets |
| **Skill edit only; no template change** | `@to-prd` says "cite vault entries as wikilinks" but template §13 still shows external-URL-only shape | Rejected — PRDs bootstrap from template; without the template update, new PRDs revert to the old shape even when the skill enforces discipline |
| **Template update only; no skill change** | Template §13 shows wikilink format but `@to-prd` step 2 still implicit | Rejected — the skill authors the template section; if the skill doesn't know to call `@vault-research`, the template section stays empty or gets filled with external URLs |
| **Both edits, delegation to `@vault-research` (chosen)** | Skill step 2 delegates; skill step 3 codifies §13 discipline; template §13 bootstraps right shape | Adopted — matches ADR-025 pattern + covers both the research mechanism and the authoring discipline |
| **Separate ADR per edit** | Split into ADR-026 (skill) + ADR-027 (template) | Rejected — the two edits are tightly coupled (template without skill is dead text; skill without template bootstraps wrong). One ADR per logical change (per `@update-horizontal` discipline) means this ADR covers the coupled pair |

## Consequences

**Enables**:

- `@to-prd` invocations in vault-mounted projects emit §13 Sources with canonical `[[wikilinks]]` + per-source reasoning
- AGENTS.md §6 one-canonical-home rule holds across PRD authoring — sources stay in `wiki/` only
- External vs. vault distinction visible in §13 — readers can see which sources the user has ingested vs. browsed
- Deferred-ingest candidates surface as actionable queue items (captured in step 3 rule #3)
- `@to-issues` (successor skill) can eventually read §13 wikilinks to cross-link GitHub issues to vault sources (future enhancement; not in this ADR's scope)

**Constrains**:

- PRDs authored before this ADR have external-URL citations for what are now vault entries. **Retro-fixing them is deferred** (not in scope here; capture as queue entry if needed during future PRD edits)
- `@vault-research` must be live (Phase 2.1 / ADR-024) — same bundle-leader dependency as ADR-025
- Template edit lives at `~/.windsurf/templates/_shared/prd-template.md` (outside this repo); PR diff doesn't catch it — same surfacing pattern as the SKILL.md files

**Deferred** (captured for future ADRs or queue):

- `@to-issues` vault-wikilink awareness — let generated issues reference §13 vault sources directly (future work; not blocking)
- Retro-fix of pre-ADR-026 PRDs' §13 citations — ad-hoc during subsequent PRD edits; no dedicated pass
- Template versioning — when template §13 changes materially, existing PRDs should know; `@docs-refresh` or `@verify-l1` could audit (future work)

## Sources consulted

- cascade-system/queue/pending-review.md M2C.3 `@to-prd` entry (pre-deletion) — source spec
- cascade-system/docs/decisions/ADR-022-obsidian-cli-selection.md (own) — CLI primitive selection
- cascade-system/docs/decisions/ADR-023-vault-layout-v3.md (own) — `wiki/sources/` canonical location + AGENTS.md §6
- cascade-system/docs/decisions/ADR-024-vault-research-skill.md (own) — delegation target
- cascade-system/docs/decisions/ADR-025-grill-me-vault-integration.md (own) — sibling delegation pattern
- cascade-system/docs/handoffs/cascade-c-obsidian.md §3 M2C.3 bullet 2 — explicit requirement for vault-wikilink §13 citations
- `~/.codeium/windsurf/skills/to-prd/SKILL.md` (own, pre-edit) — line 46 touchpoint being replaced
- `~/.windsurf/templates/_shared/prd-template.md` (own, pre-edit) — §13 block being updated

## Related ADRs

- ADR-006 (SKILL.md frontmatter schema) — applied
- ADR-009 (NNN reserve-in-INDEX-first) — applied
- ADR-014 (L1 canonical paths) — applied; skill lives at `~/.codeium/windsurf/skills/to-prd/SKILL.md`; template at `~/.windsurf/templates/_shared/prd-template.md`
- ADR-022, ADR-023, ADR-024, ADR-025 — see citations throughout

## Provenance

Authored 2026-05-04 by Cascade A as Phase 2.3 of the Sprint 2 Vertical C drain. Promoted from M2C.3 `@to-prd` queue entry (issue [#43](https://github.com/ReebalSami/cascade-system/issues/43)) per `retros/sprint-2-vertical-c-drain.md` §3 row 8.
