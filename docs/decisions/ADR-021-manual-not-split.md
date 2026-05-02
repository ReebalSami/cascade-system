# ADR-021: `docs/manual.md` stays as single-file narrative

**Status**: Accepted
**Date**: 2026-05-02
**Source**: Sprint 1.5 plan `~/.windsurf/plans/sprint-1-5-onboard-ramp-f8add2.md` §3 1.5.10 (decision deferred until 1.5.3 cheat-sheet landed); conversation `f8add2`. Decision activated post-1.5.3 (cheat-sheet) per the plan's design.

## Context

Sprint 1.5 audit identified `docs/manual.md` as a 231-line file covering 7 topics (mental model, where-things-live, invocation, how-to-start, sprint lifecycle, troubleshooting, release discipline, worked example) — *"too long to scan, too heterogeneous to split casually"*. The audit deferred a structural decision until after `docs/cheat-sheet.md` (Sprint 1.5.3) landed, on the grounds that *"once the cheat-sheet exists, redundancy in `manual.md` will be visible and the split decision becomes obvious"*.

Plan §3 1.5.10 captured the deferred decision with two options:

1. **Remove duplicates from manual; keep single file** (manual stays narrative; trim sections now covered by cheat-sheet)
2. **Split manual** into separate files: `manual.md` becomes mental-model-only; `getting-started.md`, `release-discipline.md`, `where-things-live.md` get their own files

Plan's pre-decision lean was option 1 ("probably 'remove duplicates; keep single file'") but flagged the choice as *"genuinely a decision-point"*.

## Decision

**Manual stays as a single file.** No split. Minor trims to remove sections now redundant with `docs/cheat-sheet.md` and `cascade-system/AGENTS.md` (1.5.8); cross-references added where the manual usefully points into the cheat-sheet.

Concrete trims applied in the same Sprint 1.5.10 PR:

- `§"Pointers"` (manual.md lines 80-93) — trimmed to 2-3 cross-references; AGENTS.md "Start here" + cheat-sheet's "Cross-references" section now carry the bulk of pointer-listing duty.
- Cross-references added pointing manual sections into cheat-sheet for inventory-style lookups (the cheat-sheet's tables) versus narrative-style explanations (manual's text).

Sections **explicitly retained** in manual:

- §"Where things live" (lines 7-34) — table of canonical paths + dead-paths + cross-agent fallback. Cheat-sheet has the *list*; manual has the *explanation* of why each location is canonical (e.g., the 6000-char `global_rules.md` cap, the L1-vs-L2 split rationale).
- §"How to update an L1 component" (35-41) — discipline narrative (dual-update for rules; `wc -c` enforcement). Cheat-sheet's `@propose-extension` section just lists the route; manual explains the *invariant*.
- §"How to start a new project" (43-51) and §"How to run a phase" (53-59) — kept as procedural narrative; cheat-sheet's table just lists "use `@begin`" / "use `/run-phase`".
- §"Sprint lifecycle" (61-68) — narrative chain (plan → phases → drift → review → propagate → retro). Cheat-sheet doesn't cover lifecycle.
- §"Troubleshooting" (70-78) — symptom → cause → fix table. No equivalent in cheat-sheet.
- §"Invocation cheat-sheet" (95-111) — Windsurf-docs-cited prefix semantics. Cheat-sheet doesn't go this deep on `@` vs `/` prefix mechanics. Cross-referenced from cheat-sheet.
- §"Mental model" (113-146) + §"Spawning a vertical Cascade" (148-186) + §"Release discipline" (188-232) — narrative depth + worked examples. Splitting these three sections into separate files would lose the cross-reference flow (mental model → spawning explains the L2 / L3 distinction → release discipline closes the loop on `main`-bound work).

## Alternatives considered

- **Option 2 from the plan (split into 4-5 files)** — rejected. Each split file would be 30-50 lines. That's harder to navigate than one 231-line file (now ~210 after trims): more file-opening, less cross-section context, more INDEX management overhead. Splits also create implicit boundaries between sections that currently flow naturally (e.g., "Mental model" → "Spawning a vertical" → "Release discipline" reads as one progressive narrative).
- **Hybrid: split only "Mental model" + "Spawning a vertical" into a separate `concepts.md`** — considered. Rejected because §"Mental model"'s "Horizontal vs vertical Cascade" subsection is the *bridge* into §"Spawning a vertical Cascade"; separating them breaks the bridge. If `concepts.md` later grows beyond manual's reach, revisit (see "Re-evaluation triggers" below).
- **Aggressive trim: remove §"Pointers" entirely + collapse §"Invocation cheat-sheet" into a one-line cross-ref** — rejected. §"Invocation cheat-sheet"'s Windsurf-docs citations ([chunks 338, 340, 352, 362](https://docs.windsurf.com/llms-full.txt)) are load-bearing — they document the actual mechanics of how `@` and `/` resolve. The cheat-sheet's symbolic-level summary doesn't replace the citations. Keep but maybe trim further in a future sprint if redundancy grows.
- **Keep manual fully untouched (no trims)** — rejected. Some content really is duplicated by cheat-sheet + AGENTS.md now (notably §"Pointers"). Retaining 100% would be drift-friendly.

## Consequences

**Enables:**

- A coherent narrative reading of the system: open `cascade-system/AGENTS.md` → land in cheat-sheet for inventory → land in manual for narrative depth → land in ADRs for "why" rationale. Three artifacts, three reading modes, clean delegation.
- Mental-model section keeps its bridge to spawning-a-vertical and release-discipline; future readers don't lose the progressive narrative.
- Manual's `wc -l` will hover near 200 — manageable for a single-file narrative.

**Constrains:**

- Manual stays a "long" doc by web-page standards. New sections that don't fit the narrative shape (e.g., a "configuration reference" or "worked-example library") shouldn't be added inline; they get their own files.
- The decision needs **re-evaluation** if (a) the manual exceeds ~350 lines without losing redundancy, OR (b) a section grows that legitimately wants its own file (e.g., release-discipline grows from worked-example into a procedural reference); new ADR for the re-split call.

**Re-evaluation triggers (for future sprints):**

- `wc -l docs/manual.md` exceeds 350 — automatic prompt to revisit
- A section grows to >100 lines and is referenced standalone from elsewhere — separable
- Audit-style work surfaces "I never read past line N" feedback — actionable signal

## Source

- `~/.windsurf/plans/sprint-1-5-onboard-ramp-f8add2.md` §3 1.5.10 + §2 D4 (cheat-sheet location decision; manual scope was deferred until cheat-sheet landed)
- Sprint 1.5.3 PR #33 — cheat-sheet authoring; surfaced what was actually duplicated
- Sprint 1.5.8 PR #38 — AGENTS.md update; absorbed the pointer-listing duty
- ADR-003 (strict docs structure) — placement rules for `docs/` root files; cheat-sheet.md added to allowed-files list in Sprint 1.5.4
- `cascade-system/docs/cheat-sheet.md` — companion artifact this ADR coordinates with
- `cascade-system/AGENTS.md` — companion artifact (also updated in 1.5.8)
