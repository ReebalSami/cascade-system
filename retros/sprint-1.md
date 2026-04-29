# Sprint 1 Retrospective — L1 Foundation

**Cascade**: A (horizontal)
**Status**: closed
**Date closed**: 2026-04-29
**Plan ref**: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 1
**Execution plan**: `~/.windsurf/plans/sprint-1-execution-abf225.md`
**Order ADR**: `docs/decisions/ADR-005-sprint-1-execution-order.md`
**Mid-sprint amendment ADR**: `docs/decisions/ADR-004-shared-scaffold-pattern.md` (affects M1.4 design)

## Milestone outcomes (appended live as each closes)

| Order | ID | Issue | Status | Artifact | Notes |
|---|---|---|---|---|---|
| 1 | M1.3 | #4 | ✓ closed | `~/.windsurf/contracts/phase-taxonomy.md` | Schema novel to system; no upstream pattern at this granularity. Includes 4 reader contracts + versioning policy + 2 examples (python-ml-uv + nextjs-app generality check). |
| 2 | M1.1 | #2 | ✓ closed | 7 rules in `~/.windsurf/rules/` | Promoted: anti-laziness, no-half-knowledge, context7-and-docs-first (model_decision), make-sure-it-works, no-terminal-oneline-scripts, be-honest-direct-critical, clean-project-structure. Deferred 4 (logged to queue). |
| 3 | M1.2 | #3 | ✓ closed | `~/.windsurf/skills/grill-me/SKILL.md` + ADR-006 | First skill — locked SKILL.md frontmatter schema (closes plan §11 open Q). Adapted superpowers brainstorming + mattpocock grill-me; removed visual-companion + writing-plans handoff; aligned to our phase taxonomy. |
| 4 | M1.5 | #6 | ✓ closed | `~/.windsurf/workflows/run-phase.md` | Generic dispatcher consuming M1.3 contract. 9-step procedure (locate → validate → resolve → entry → invoke → verify → exit → milestone → next). Listing mode + error-handling table. Unblocks M1.4. |
| 5 | M1.4 | #5 | ✓ closed | `~/.windsurf/workflows/start-project.md` + `~/.windsurf/templates/_shared/scaffold/` (9 files) | 14-step orchestrator implementing ADR-004 two-pass scaffold + ADR-001 gh/MCP routing. Includes dry-run mode and failure-handling table. Created `_shared/scaffold/` initially per ADR-004 consequences. |
| 6 | M1.6 | #7 | ✓ closed | `~/.windsurf/skills/{to-prd,to-issues}/SKILL.md` + `templates/_shared/prd-template.md` | 13-section PRD template; /to-prd 9-step (brainstorm → PRD); /to-issues 10-step (PRD §11 → GitHub issues + milestones + Project v2). Hard gates per skill. ADR-001 routing honored. Pulled in spirit of deferred `github-project-management.md` rule. |
| 7 | M1.7 | #8 | ✓ closed | `~/.windsurf/workflows/recalibrate.md` + `~/.windsurf/rules/plan-drift-watcher.md` | Passive rule (6 drift signals) + active 8-step workflow. Coupling: rule notices, workflow resolves. Shape-based reporting per `no-quantity-over-shape`. Per-finding triage; ADR for material drift. |
| 8 | M1.8 | #9 | ✓ closed | `~/.windsurf/skills/sync-github/SKILL.md` | Idempotent PM agent. 8-step procedure (verify → read → compute columns + labels + milestones → detect naked commits → diff → apply → report). ADR-001 routing strict. Distinct from `/recalibrate`: bookkeeping vs design drift. Closes deferred `github-project-management.md` from M1.1 in skill form. |
| 9 | M1.9 | #10 | ✓ closed | `~/.windsurf/skills/sprint-review/SKILL.md` + 2 rules + retro template | Heartbeat skill (10-step). `sprint-review-prompt` (model_decision passive trigger). `bidirectional-learning-pipe` (always_on ambient capture). Retro template aligned to queue format. M1.9-before-M1.10 handoff: first `/sprint-review` stages L1 promotions as Proposed ADRs. |
| 10 | M1.10 | #11 | ✓ closed | `~/.windsurf/skills/{write-skill,update-horizontal}/SKILL.md` + `~/.windsurf/workflows/add-project-type.md` | Self-maintenance toolkit. Adapted mattpocock `write-a-skill` (process structure, description rules) → richer per ADR-006. `/update-horizontal` is the single L1-mutation channel (anti-divergence check + change ADR mandatory). `/add-project-type` validates against M1.3 contract + tests via `/start-project --dry-run`. End-to-end flow now closed: M1.9's first review can now hand off to /update-horizontal directly. |
| 11 | M1.11 | #12 | ✓ closed | `~/.windsurf/skills/docs-refresh/SKILL.md` + 3 architecture templates | Idempotent docs bookkeeping (mirrors `/sync-github` for docs). 9-step (locate → read structure → validate placement → regenerate INDEX → audit diagrams → audit refs → report → apply with per-file approval → final). Diagram templates: README + 2 Mermaid examples. Meta-repo retros-at-root special case respected. |

## What worked

- **Critical-path-first ordering** (per ADR-005): authoring M1.3 first unblocked Sprint 2 verticals from the moment milestone 1 closed. Subsequent milestones consumed the contract cleanly. The deviation from issue numbering (M1.5 before M1.4) eliminated forward-references and made M1.4's hand-off step resolvable.
- **`adapt-from-all` discipline produced cleaner artifacts**: every authored skill carried `sources_consulted` + `adapted_for` frontmatter; the cross-checks against `superpowers/`, `mattpocock-skills/`, and `claude-skills/` repeatedly caught simpler or stronger upstream patterns we'd otherwise have re-invented.
- **Coupling design pairs** (rule + workflow / passive + active): `plan-drift-watcher` ↔ `/recalibrate`, `sprint-review-prompt` ↔ `/sprint-review`, `bidirectional-learning-pipe` ↔ `/sprint-review` queue drain. Notice/resolve separation kept each artifact small and single-purpose.
- **Hard-gate pattern**: every state-mutating skill (PRD, issues, sync-github, update-horizontal, sprint-review, docs-refresh) preserves a "do not act until user approves" gate. Multiple gates were exercised during this sprint's authoring; none felt ceremonial.
- **MCP-for-issues, gh-CLI-for-projects** (ADR-001): once switched, all subsequent issue close+comment cycles ran cleanly. Ten consecutive milestones closed without a single approval-loop failure on the GitHub mutation step.
- **Two-pass scaffold** (ADR-004): authored M1.4 against an ADR that landed mid-sprint from another Cascade. Picking up a peer-authored ADR mid-sprint and threading it through a downstream milestone worked smoothly — the ADR + milestone-issue convention carried the design intent without chat-history dependency.

## Friction / surprises

- **Disk full mid-M1.2** — the workspace `/Users` filled to 100% (104 MB free) between M1.1 and M1.2 writes. Both M1.2 file creates failed with `no space left on device`. Recovered after the user freed 70 GB (`Library/Caches`). **Observation**: long-running authoring sessions need disk-headroom checks or Cascade should surface usage proactively. Not a rule, but worth keeping in mind for vertical Cascades that run longer.
- **Concurrent ADR-004 collision**: mid-sprint, another Cascade authored `ADR-004-shared-scaffold-pattern.md` while I was drafting my own ADR-004 for execution order. The system only flagged the conflict at `INDEX.md` edit time. I renumbered mine to ADR-005 with a `Note on numbering` line. **Observation**: ADRs are claim-by-write — no allocation gate. For multi-Cascade work, either (a) reserve numbers in `INDEX.md` before authoring, or (b) accept post-hoc renumber as a normal pattern. This time post-hoc renumber was fine.
- **Long-comment GitHub close via `gh` CLI failed user approval twice** (M1.1). Switched to MCP (`mcp3_add_issue_comment` + `mcp3_issue_write` with state=closed) per ADR-001's prescribed routing. Ran cleanly thereafter. **Observation**: stuffing multi-line bodies into `gh ... --comment "..."` is exactly the `no-terminal-oneline-scripts` violation the rule was authored to forbid. The rule was right; my first reflex (using `gh`) was wrong. Counts as the rule earning its keep on day one.
- **M1.1 deferral pattern**: 4 portfolio-website rules (`skill-by-skill`, `local-demo-before-push`, `config-yaml-first`, `github-project-management`) were considered for L1 but deferred. By Sprint 1's end, two were absorbed into shipped skills (`/sync-github` carries the github-project-management spirit; `/to-issues` carries the slice/label discipline; `make-sure-it-works` absorbed local-demo-before-push). **Pattern observation**: not every "rule" is a rule; some are skills-in-waiting. The deferral discipline (queue → re-evaluate) caught this correctly.
- **Skill length norms**: mattpocock heuristic suggests "split if SKILL.md > 100 lines". Mine landed in the 150–300 line range consistently. Logging to queue for `/sprint-review` consideration — should our heuristic be different (200? 400?), or is splitting genuinely preferable here?

## L1 change proposals (carry to Sprint 2's `/sprint-review`)

`/update-horizontal` (M1.10) is now operational, so future L1 changes can apply directly. For this Sprint 1 retro, staging the following as **observations not yet promoted** — Sprint 2's `/sprint-review` will revisit them with broader cross-vertical evidence:

| Proposal | Target | Status | Trigger for promotion |
|---|---|---|---|
| Codify SKILL.md length heuristic (target ~150 lines, split when domain-distinct) | `~/.windsurf/rules/<new>` or amend `adapt-from-all` | Observed; not yet proposed | A vertical reports either "too cramped" or "too sprawling" — concrete pain |
| Multi-Cascade ADR-numbering coordination (reserve before authoring vs accept post-hoc renumber) | `~/.windsurf/rules/<new>` or amend `document-as-you-go` | Observed; not proposed | A second collision happens and post-hoc renumber proves costly |
| Disk-headroom check before long authoring sessions | Tooling or operational note, not a rule | Observed; defer | If recurrence — current incident is one-off |

The 4 M1.1 deferrals (already in queue) get re-evaluated with vertical evidence:

| Deferred rule | Re-eval trigger | Likely outcome at Sprint 2 review |
|---|---|---|
| `skill-by-skill` | Any vertical reports focus loss | Stay deferred — milestone discipline + `no-quantity-over-shape` cover it so far |
| `local-demo-before-push` | `make-sure-it-works` proves too abstract | Likely permanent absorption |
| `config-yaml-first` | First nextjs-app project starts | Move to nextjs-app L3 template (not L1) |
| `github-project-management` | `/sync-github` duplicates per-repo logic | Likely permanent absorption (already partially absorbed) |

## ADRs written this sprint

| ID | Title | Trigger |
|---|---|---|
| [ADR-005](../docs/decisions/ADR-005-sprint-1-execution-order.md) | Sprint 1 execution order | Pre-sprint planning; renumbered from ADR-004 due to concurrent ADR landing |
| [ADR-006](../docs/decisions/ADR-006-skill-frontmatter-schema.md) | SKILL.md frontmatter schema | M1.2 — first authored skill required closing plan §11 open question |

ADR-004 (`_shared/scaffold/` pattern) was authored by another Cascade mid-sprint and consumed by M1.4. Counted toward Sprint 1 ADR output even though authored externally.

## Carry-forward to Sprint 2

- **Sprint 2 = vertical-spawn sprint**. Cascades B (template `python-ml-uv`) and C (Obsidian integration) start from the handoffs:
  - `~/Projects/cascade-system/docs/handoffs/cascade-b-template-python-ml-uv.md`
  - `~/Projects/cascade-system/docs/handoffs/cascade-c-obsidian.md`
- **First real `/sprint-review`** runs at Sprint 2's close, reviewing both verticals + the L1 observations table above. With `/update-horizontal` now operational, approved L1 promotions apply directly.
- **First real `/start-project --dry-run`** (M1.4 + M1.10's testing step) runs when Cascade B authors the `python-ml-uv` L3 template — that's the first integration test of the whole bootstrap stack.
- **First real `/recalibrate` exercise**: likely during Cascade D (thesis project) once the python-ml-uv L3 has soaked under real research work.
- **Reference repos cleanup**: `refs/` consumed disk during the disk-full incident. Consider a `.gitignore`-aware archive policy for refs once stable patterns are extracted (Sprint 3+).

## Definition of done for Sprint 1

- [x] All M1.x milestones complete (issues #2–#12 closed)
- [x] Final retro consolidated (this file)
- [x] Sprint 2 handoff docs authored: `cascade-b-template-python-ml-uv.md`, `cascade-c-obsidian.md`
- [x] L1 observations staged for Sprint 2 review (above)
