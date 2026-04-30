# ADR-017: `@propose-extension` as single intake channel for system extensions

**Status**: Accepted
**Date**: 2026-04-30
**Source**: Sprint 2 prep handoff plan (`~/.windsurf/plans/fresh-cascade-handoff-bff065.md` Prompt C Phase 1) authored a "system-extension meta-skill" requirement; pre-Sprint-2 audit (`~/.windsurf/plans/pre-sprint-2-review-4fa159.md` §C.2) flagged its scope as undefined; this ADR locks the scope and codifies the convention.

## Context

Before this skill, adding-or-modifying any cascade-system artifact required the user (or Cascade) to know which authoring path applied:

- New skill → invoke `@write-skill`
- New rule → dual-author (long-form archive + `global_rules.md`, ≤ 6000 chars)
- New workflow → write to the right global / workspace / template path with a `description:` frontmatter
- New contract → write to `~/.windsurf/contracts/<name>.md`
- New L3 project type → invoke `/add-project-type`
- New shared scaffold asset → write to `~/.windsurf/templates/_shared/<path>`
- New AGENTS.md → write to workspace root
- Modify existing L1 artifact → invoke `@update-horizontal`
- Capture-only (defer build) → append to `~/Projects/cascade-system/queue/pending-review.md`

Nine routes. No single discoverable entry point. No consistent ADR-yes-no decisioning. No first-class build-vs-capture toggle. The Phase 1 prompt at `fresh-cascade-handoff-bff065.md` listed only six of these (L1 + L3 + contracts), missing L2 entirely (which `portfolio-website/.windsurf/` heavily uses — 17 rules, ~16 skills, 11 workflows), missing AGENTS.md (which already exists at `portfolio-website/AGENTS.md`), and missing shared scaffold assets.

The L1 layer also has a hard discipline gap: `@update-horizontal` requires that L1 mutations route through it (the "single channel that mutates L1"), but there was no analogous channel for **new** L1 / L2 / L3 / AGENTS.md authoring. New artifacts could be authored ad-hoc, bypassing `adapt-from-all`, the ADR gate (`document-as-you-go`), and the queue/build decision.

## Decision

`@propose-extension` is the **single intake channel** for any cascade-system artifact mutation, new or modify, across all layers (L1, L2, L3, workspace AGENTS.md). It is a meta-skill that:

1. Interviews the user with a fixed 5-question script (build-vs-capture, artifact type, layer, problem, activation/trigger).
2. Runs an `adapt-from-all` consultation pass over `~/Projects/cascade-system/refs/`.
3. Decides whether an ADR is needed (using the `grill-with-docs` 3-test: hard-to-reverse, surprising-without-context, real-trade-off).
4. Presents a **route + (optional) ADR draft** for explicit user approval.
5. Dispatches to the correct authoring path per the route table below.
6. Runs `@verify-l1` and commits via `/commit`.

The skill **never directly authors** a SKILL.md (it dispatches to `@write-skill`) and **never directly mutates** an existing L1 artifact (it dispatches to `@update-horizontal`). Direct file writes happen only for routes that have no dedicated authoring skill (workflow files, rule archive entries, contracts, AGENTS.md, shared scaffold assets).

### Route table

| Artifact | Layers | Route |
|---|---|---|
| Rule | L1 (dual) / L2 / L3 | L1: dual-author archive + `global_rules.md` (run `wc -m` ≤ 6000). L2: `<project>/.windsurf/rules/<name>.md`. L3: `~/.windsurf/templates/<type>/rules/<name>.md` |
| Skill | L1 / L2 / L3 | Invoke `@write-skill` |
| Workflow | L1 / L2 / L3 | L1: `~/.codeium/windsurf/global_workflows/<name>.md` (per ADR-016). L2: `<project>/.windsurf/workflows/<name>.md`. L3: `~/.windsurf/templates/<type>/workflows/<name>.md`. `description:` frontmatter required. |
| Contract | L1 | `~/.windsurf/contracts/<name>.md` |
| L3 project type | L3 | Invoke `/add-project-type` |
| Shared scaffold asset | L1 | `~/.windsurf/templates/_shared/<path>` |
| AGENTS.md | workspace root | `<project>/AGENTS.md` |
| L1 mutation of existing | L1 | Invoke `@update-horizontal` |
| Capture-only | queue | Append to `~/Projects/cascade-system/queue/pending-review.md` |

## Alternatives considered

- **Direct authoring per artifact (status quo)** — rejected. No consistency in build-vs-capture decisioning, no consistent ADR gate, no `adapt-from-all` enforcement at the moment of authoring. Drift accumulated: 6 routes documented in one prompt, 9 actually present in the system.

- **Extend `@write-skill` to dispatch all artifact types** — rejected. Conflates authoring (`@write-skill`'s job) with dispatch (`@propose-extension`'s job). `@write-skill`'s per-section review discipline doesn't fit non-skill artifacts (a contract or AGENTS.md doesn't have a "frontmatter / when-to-use / hard-gate / procedure / anti-patterns" structure). Forcing one shape on all artifacts loses fidelity.

- **Make it a workflow not a skill** — rejected. Dispatch needs an interview (skill-shaped — auto-activates by description match, walks the user through questions). Workflows can't auto-activate; they're slash-only per ADR-015. A user saying "I want to add a new rule" should auto-route into the dispatcher, not require them to remember `/propose-extension`.

- **Keep the prompt's 6-route scope (L1 + L3 + contracts only)** — rejected. Empirical evidence (this conversation's enumeration): L2 is heavily used in real projects, AGENTS.md exists, shared scaffold assets exist. A dispatcher that doesn't know about a third of the system's artifacts forces users to remember the exceptions, defeating the "single intake channel" purpose.

- **Merge with `@update-horizontal` (one channel for both new and modify)** — rejected. `@update-horizontal` is rightly conservative (re-consults upstreams, drafts diffs, gates on diff approval) — that overhead isn't appropriate for first-time authoring of an L2 rule or AGENTS.md edit. Keep the channels distinct; `@propose-extension` dispatches *to* `@update-horizontal` when the route is "L1 mutation of existing".

## Consequences

**Enables:**

- A discoverable single entry point for all system extensions; auto-activates on phrases like "I want to add a new rule", "let's integrate X", "extend the system with Y".
- First-class build-vs-capture decision (the queue path stops being a forgotten alternative).
- Consistent ADR gate via the `grill-with-docs` 3-test at every authoring opportunity.
- `adapt-from-all` enforced at the moment of authoring (not retrofitted after).
- L2 + AGENTS.md become discoverable artifact types (the prompt missed them).

**Constrains:**

- All future system extensions are expected to route through `@propose-extension`. Direct authoring is allowed but signals process drift; `@verify-l1` and future heartbeats may flag it.
- The skill body must be kept ≤ 150 lines (per `@write-skill`'s split heuristic) — when route count grows beyond ~10, split the route table into a sub-doc.
- Pre-existing artifacts authored before this skill (Sprint 1's 9 skills, 5 workflows, 14 rules, 1 contract, the `_shared/scaffold/`) are **not** retroactively migrated — they stay as-is per ADR-011 retention.

**Empirical test:** the next two parked-task spawns (`release-manager` brainstorm + `branch-and-pr-required` / `know-your-hardware`) should route through `@propose-extension`. If a fresh Cascade authoring those items bypasses this skill, that's a discoverability bug worth a sprint-review item.

**Bootstrap note:** `@propose-extension` itself was authored without routing through itself (chicken-and-egg). The Sprint 2 prep Cascade authored it directly via this ADR + plan. Future meta-skills face the same bootstrap; convention is: bootstrap meta-skills get their own ADR, then become routable for everything else.

## Source

- `~/.windsurf/plans/fresh-cascade-handoff-bff065.md` Prompt C Phase 1 — the original spec
- `~/.windsurf/plans/pre-sprint-2-review-4fa159.md` §C.2 — flagged scope undefined
- `~/.windsurf/plans/l1-discovery-repair-4fa159.md` Phase H bullet #4 — the handoff trigger
- `~/.windsurf/plans/propose-extension-skill-127742.md` — this Cascade's authoring plan
- ADR-006 — skill frontmatter schema this skill enforces (via `@write-skill` delegation)
- ADR-014 — L1 canonical storage paths the routes target
- ADR-015 — `@` skills vs `/` workflows discipline in cross-references
- ADR-016 — `global_workflows/` path correction reflected in the workflow route
- `~/Projects/cascade-system/refs/mattpocock-skills/skills/engineering/grill-with-docs/SKILL.md` — sparing-ADR 3-test adopted into the procedure
