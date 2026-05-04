# ADR-027: `/recalibrate` 7th drift signal (Vault drift)

**Status**: Accepted
**Date**: 2026-05-04
**Plan**: `~/.windsurf/plans/sprint-2-vertical-c-drain-86a684.md` Phase 2.4
**Source retro**: `~/Projects/cascade-system/retros/sprint-2-vertical-c-drain.md` §3 (row 9) + §4 (Phase 2.4)
**Source queue entry**: M2C.3 (`Sprint 2 — cascade-system — Cascade C — M2C.3 (`/recalibrate` vault-drift 7th signal)`)
**Related**: ADR-022 (Obsidian CLI), ADR-023 (vault layout v3 — AGENTS.md §11 supersession), ADR-024 (`@vault-research` delegation target), ADR-025 + ADR-026 (sibling M2C.3 integration-target ADRs)
**Issue**: [#43](https://github.com/ReebalSami/cascade-system/issues/43) (M2C.3)

## Context

`/recalibrate` workflow (at `~/.codeium/windsurf/global_workflows/recalibrate.md` per ADR-016) pre-edit had **zero Obsidian touchpoint**. Its 6-axis drift table covered PRD/GitHub/git only: scope creep, unreflected work, issue abandonment, artifact lag, milestone-state mismatch, phase mismatch.

Handoff `docs/handoffs/cascade-c-obsidian.md` §3 M2C.3 bullet 3 proposed *"surface drift between vault and PRD as a 7th drift signal"* — a net-new drift axis, not an edit to an existing one.

Post-ADR-023 the vault has a deterministic spine (`_meta/` + `originals/` + `raw/` + `wiki/`) with dated entries (`_meta/log.md`, frontmatter `date:` fields across `originals/` + `wiki/`) that makes *"vault newer than PRD, relevant but uncited"* mechanically computable via the Obsidian CLI. Post-ADR-024 there's a ranked-query skill (`@vault-research`) to delegate topic scoping to.

## Decision

**Three coupled edits** to the workflow + one frontmatter update, all in this ADR:

### Edit 1 — Step 1 heading + source #4

Change heading from *"Load three sources of truth"* to *"Load four sources of truth"*. Add the fourth source:

```
- Vault state (if Obsidian CLI available per ADR-022; graceful-skip otherwise):
  invoke @vault-research <prd-topic> (ADR-024) per major topic extracted
  from PRD §7 solution overview + §11 slices to get ranked topically-relevant
  vault entries. Then load their frontmatter date: fields via obsidian read
  to compare against PRD Date. Also scan phases.yaml for obsidian:// artifact
  paths (per M1.3 phase-taxonomy contract) and verify their referenced vault
  notes resolve via obsidian files.
```

### Edit 2 — Step 2 drift-axes table (add 7th row)

Change heading from *"Compute drift across 6 axes"* to *"Compute drift across 7 axes"*. Add:

```
| Vault drift | Two sub-signals: (a) wiki/sources/<type>/ or
  wiki/concepts/<domain>/<sub>/ entries with frontmatter date: newer than
  PRD Date that are topically relevant (from step 1's @vault-research result
  — MOC-backlink or tag match) but not cited in PRD §13 as [[wikilinks]];
  (b) obsidian:// artifact paths in phases.yaml (or [[wikilinks]] cited in
  PRD §13) whose referenced vault notes have moved or been superseded per
  ADR-023 AGENTS.md §11 (tombstone on old path redirects to new path). |
```

Also update step 3 drift-report template to include a **"Vault drift"** section showing both sub-signals. Update *"If all six are clean"* → *"If all seven are clean"*.

### Edit 3 — Step 5 apply-actions table (add matching row)

```
| Vault drift | Either: (a) invoke @to-prd re-edit to add missing
  [[wikilinks]] to PRD §13 with per-source reasoning (per ADR-026
  discipline); or (b) if a cited vault note was superseded, update the
  [[wikilink]] target path per ADR-023 AGENTS.md §11; or (c) if phases.yaml
  artifact path moved, update the path inline (no ADR needed for a rename). |
```

### Edit 4 — Provenance section

Add ADR-022 / 023 / 024 / 026 / 027 citations to the "Sources consulted" list. Add M2C.3 amendment reference to the Plan ref line.

## Why delegation to `@vault-research`

Same rationale as ADR-025 + ADR-026. `@vault-research` owns the ranking algorithm; three integration targets delegate rather than reimplement. One bug-fix in the ranking algorithm → one edit to `@vault-research`, not three.

**But**: `/recalibrate` needs a *post-filter* on `@vault-research`'s result (filter by `date > PRD.Date` + filter by *"not cited in PRD §13"*). That's not `@vault-research`'s responsibility — it surfaces topical relevance; `/recalibrate` adds the drift-specific filters. Clean separation: the skill ranks; the workflow filters for its use case.

## Why a 7th axis, not an edit to an existing axis

Considered mapping Vault drift into **Artifact lag** (axis 4). Rejected:

- Artifact lag computes *"PRD vs commit history"* — outputs a single drift signal on the PRD itself
- Vault drift computes *"PRD §13 citations vs vault entries"* — outputs per-entry signals on the PRD's reference graph

Different inputs, different outputs, different apply-actions. Separate axis is correct.

## Why workflow, not skill

`/recalibrate` has always been a workflow (manual `/<name>` invocation per ADR-015), not a skill. The 7th signal doesn't change that. Workflows don't have `sources_consulted` / `adapted_for` frontmatter (per the workflow vs skill split); ADR citations live in the Provenance section instead.

## Alternatives considered

| Candidate | Shape | Verdict |
|---|---|---|
| **Keep 6 axes; ignore vault drift** | Status quo | Rejected — ADR-023 shipped the vault; PRD §13 citations rot silently otherwise |
| **Map vault drift into Artifact lag axis** | Expand axis 4 to cover vault staleness | Rejected — different inputs, different outputs, different apply-actions; conflation reduces clarity |
| **Author a separate `/vault-drift` workflow** | One workflow per drift category | Rejected — `/recalibrate`'s value is the unified triage loop; splitting fragments user mental model |
| **Inline vault CLI calls (no `@vault-research` delegation)** | Workflow calls `obsidian search` directly | Rejected — duplicates ADR-024's ranking across three integration targets (ADR-025 pattern applies here too) |
| **7th axis with `@vault-research` delegation + workflow-local post-filter (chosen)** | Delegates ranking; workflow adds drift-specific filters | Adopted — matches sibling ADRs' delegation pattern; preserves `/recalibrate` as unified triage |

## Consequences

**Enables**:

- `/recalibrate` now catches vault drift — PRDs stay cited-current as the vault grows
- Phases.yaml `obsidian://` artifact path moves caught mechanically (pre-ADR-027 they rotted silently)
- AGENTS.md §11 supersession tombstones resolved via the apply-action — when a vault note is superseded, the `[[wikilink]]` in PRD §13 gets updated, not left dangling
- All three M2C.3 integration targets (`@grill-me` / `@to-prd` / `/recalibrate`) now share the `@vault-research` delegation pattern — one vault-access architecture, three consumers

**Constrains**:

- Vault drift computation requires PRD `Date` + vault frontmatter `date:` fields — if a PRD or vault entry lacks `date:`, that entry is skipped from drift computation (document-as-you-go discipline should prevent this; monitor via `@verify-l1`)
- `/recalibrate` runs take slightly longer in vault-mounted projects (one `@vault-research` call per major PRD topic). Graceful-skip when CLI unavailable — non-vault projects unaffected

**Deferred** (future queue captures):

- `@to-issues` vault-wikilink awareness (mentioned in ADR-026 deferred list) — would let the Vault drift apply-action's option (a) automatically re-file issue descriptions too
- Empirical calibration of "topically relevant" threshold — `@vault-research` returns top 3–7; how many of those should surface as drift? v1 uses all. Revisit if noise ratio is high
- `phases.yaml` schema validation of `obsidian://` paths — could live in `@verify-l1` (Phase 2.6 promotion) or as a new contract check

## Sources consulted

- cascade-system/queue/pending-review.md M2C.3 `/recalibrate` entry (pre-deletion) — source spec
- cascade-system/docs/decisions/ADR-022-obsidian-cli-selection.md (own) — CLI primitives
- cascade-system/docs/decisions/ADR-023-vault-layout-v3.md (own) — vault spine + AGENTS.md §11 supersession semantics
- cascade-system/docs/decisions/ADR-024-vault-research-skill.md (own) — delegation target
- cascade-system/docs/decisions/ADR-025-grill-me-vault-integration.md (own) — sibling delegation pattern
- cascade-system/docs/decisions/ADR-026-to-prd-vault-sources-integration.md (own) — citation discipline this workflow's apply-action enforces
- cascade-system/docs/handoffs/cascade-c-obsidian.md §3 M2C.3 bullet 3 — explicit 7th-signal requirement
- `~/.codeium/windsurf/global_workflows/recalibrate.md` (own, pre-edit) — 6-axis table being extended

## Related ADRs

- ADR-001 (gh + MCP routing) — applied; step 5 apply-actions still route through `@to-prd`
- ADR-009 (NNN reserve-in-INDEX-first) — applied
- ADR-014 (L1 canonical paths) — applied; workflow lives at `~/.codeium/windsurf/global_workflows/recalibrate.md`
- ADR-015 (skill vs workflow invocation) — applied; `/recalibrate` stays workflow-syntax
- ADR-016 (workflow canonical path correction) — applied
- ADR-022 / 023 / 024 / 025 / 026 — see citations throughout

## Provenance

Authored 2026-05-04 by Cascade A as Phase 2.4 of the Sprint 2 Vertical C drain. Promoted from M2C.3 `/recalibrate` queue entry (issue [#43](https://github.com/ReebalSami/cascade-system/issues/43)) per `retros/sprint-2-vertical-c-drain.md` §3 row 9. Third and final M2C.3 integration-target ADR, completing the vault-drift axis of the architectural trio (M2C.3 + M2C.4 + M2C.5).
