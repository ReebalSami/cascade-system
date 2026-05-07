# Sprint 2 — Vertical B — `python-ml-uv` L3 template — Retro

**Project**: cascade-system (meta-repo)
**Cascade**: B (vertical, owns the `python-ml-uv` L3 project-type template)
**Milestone**: GitHub `Sprint 2 Vertical B — python-ml-uv L3 template` (closes on this retro's merge)
**Status**: closed
**Date opened**: 2026-05-07
**Date closed**: 2026-05-07
**Plan ref**: `~/.windsurf/plans/cascade-b-autonomous-run-a7ae44.md` (autonomous-run plan; preceded by legacy `~/.windsurf/plans/cascade-b-kickoff-{6bbb3c,a7ae44}.md` per ADR-011 supersession-over-deletion)
**Handoff**: `docs/handoffs/cascade-b-template-python-ml-uv.md`
**Issues closed in this milestone**: M2B.1 #75, M2B.2 #76, M2B.3 #77, M2B.4 #78, M2B.5 #79, M2B.6 #80, M2B.7 #81 (this PR), M2B.8 #83 (next PR)
**PRs**: 7 squash-merged into `main` via `@release-manager` (#84 M2B.1 brainstorm + #85 B11 queue capture; #86 M2B.2; #87 M2B.3; #88 M2B.4 [bundled both skills]; #89 M2B.5 [bundled both rules]; #90 M2B.6 validation + fixes) plus this retro PR + the upcoming M2B.8 handoff PR

> **Snapshot semantics**: per ADR-010-adjacent clarity, counts and file lists below reflect state at vertical close.

## What worked

- **Brainstorm-locked decisions held all the way through.** B1–B11 from `docs/prompts/stages/02-brainstorm-python-ml-uv.md` survived M2B.2 → M2B.6 unchanged. Carry-forward table at brainstorm line 291 served as the M2B.2–M2B.5 spec verbatim. Evidence: every milestone's PR cited the brainstorm B# anchor; no decision had to be re-litigated.
- **Forward-reference pattern in `phases.yaml`.** M2B.2 forward-referenced 4 skills (`@literature-review`, `@run-experiment`, `@tdd`, `@writeup`) — 2 authored at M2B.4, 2 queued for L1 promotion. The contract (`~/.windsurf/contracts/phase-taxonomy.md` v1) supports this natively (the `skill:` field is a string; existence-check happens at `/run-phase` consumption). Pattern matches the contract example §7. No friction.
- **`@release-manager` flow scaled cleanly across 7 PRs.** Branch → commit → push → PR → CI-watch (no-CI skip) → squash-merge → cleanup ran ~7 times without issue. `/commit` workflow handled all multi-paragraph commit bodies safely (no `no-terminal-oneline-scripts` violations). Standing-approval autonomous mode (per user's explicit blanket approval) avoided per-PR confirmation overhead.
- **`@kickoff` lifecycle detection landed clean.** Detected case `mid-execution` (M2B.1 closed, M2B.2–M2B.8 open), filed no missing milestones (already filed by a prior session), asked one focused question, dispatched into M2B.2. Compare to Vertical C's 10-plans-for-6-milestones churn — Vertical B produced 2 plan files for 7 milestones (kickoff + autonomous run).
- **Single-PR-per-milestone audit trail.** Even when handoff §3 directed "separate PR per skill" (M2B.4), bundling into one PR with a non-empty repo-side change (cheat-sheet update + queue entry) preserved auditability without manufacturing empty diffs. The L1 storage gap (`~/.windsurf/` not git-tracked) made this the natural shape; documented as deviation in queue.
- **M2B.6 integration test caught real defects, exactly as designed.** First end-to-end exercise of `_shared/scaffold/` two-pass + `/start-project` workflow + python-ml-uv template surfaced two real defects (PEP 503-invalid placeholder; mypy missing-stubs on conditional import). Both fixed in the same PR. The milestone earned its existence.

## Friction / surprises

- **`__pkg__` placeholder violated PEP 503** — `uv sync` rejected `name = "__pkg__"` because PEP 503 requires names to start AND end with a letter or digit. Surfaced at M2B.6 dry-run; fixed by switching to dual-token convention (`your-pkg` kebab + `your_pkg` snake, mirroring `scikit-learn`/`sklearn`). The M2B.3 author (this Cascade) chose `__pkg__` for visual obviousness without verifying PEP 503; M2B.1 brainstorm B11 didn't drill into placeholder convention. Captured in queue (M2B.3 entry's M2B.6 update subsection).
- **mypy fails on conditional optional-dep imports** — `seeding.py`'s `import numpy as np` inside `try / except ImportError` (per brainstorm B8=C) tripped `make typecheck` because mypy requires stubs for every import regardless of guard context. Fixed by adding `[[tool.mypy.overrides]]` for `numpy` to `pyproject.toml`. Generalizes to any optional ML dep under the same pattern. Queued.
- **L3 skill body line-count overshot brainstorm estimate** — B6 estimated `@literature-review` + `@run-experiment` at "~80–100 line body, fits ≤150 line norm". First-draft `@literature-review` came out at 194 lines (164 body + 30 frontmatter); trimmed to 138 by compressing inline output-format examples. ADR-006 frontmatter overhead (sources_consulted + adapted_for + activation + phase + produces_artifacts + requires_skills) systematically takes ~30 lines for composing skills. Queued as `@write-skill` heuristic update.
- **Single-PR vs separate-PR discipline (handoff §3)** — handoff §3 M2B.4 directed "Each skill's authoring lands as a separate PR via `@release-manager`". This vertical bundled both skills into one PR because L3 artifacts live outside the cascade-system repo (per-skill PRs would have empty diffs). Same applied to M2B.5 rules. Documented as deviation in queue; pattern matches M2B.2 + M2B.3 (single PR per milestone). The L1 storage gap (queue entry "Sprint 1 review — M-2") is the upstream cause.

## Learnings

- **Insight**: First L3 template authoring is a bona fide integration test for `/start-project` + `_shared/scaffold/` + the contract. M2B.6 surfaced two real defects that no amount of M2B.1–M2B.5 self-review would have caught (placeholder-validity is empirically discoverable; mypy-conditional-import is empirically discoverable). The milestone earned its design role.
- **Source**: M2B.6 dry-run output; `queue/pending-review.md` "M2B.3 update" subsection + new "M2B.6 mypy + conditional optional-dep imports" entry.
- **Proposed L1 change**: capture an `@verify-l3-template` skill (or extend `@verify-l1`) that runs `make test` + `make lint` + `make typecheck` against any L3 template's scaffold at template-authoring time. Catches scaffold-level defects before they reach a consumer.
- **Project-local action**: none (vertical is closing).

- **Insight**: The brainstorm's 11 questions (B1–B11) framed every downstream milestone (M2B.2–M2B.6) and most queue entries. Brainstorm depth → vertical efficiency was super-linear: M2B.2–M2B.5 each took roughly one PR's worth of mechanical work.
- **Source**: All M2B.x PR commit bodies cite specific brainstorm B# anchors. M2B.4 + M2B.5 issue body updates pull verbatim from brainstorm carry-forward line numbers.
- **Proposed L1 change**: none — the pattern already works. `@grill-me` should keep producing this depth.
- **Project-local action**: none.

- **Insight**: Standing-approval autonomous mode (one explicit user grant + self-review at each merge gate) is dramatically more efficient than per-PR confirmation, AND quality didn't degrade. Self-review caught nothing the user-review would have caught differently for these milestones.
- **Source**: User instruction at session start ("you got all my approvales in advance, under the condition you make the review yourself"); 6 PRs landed without per-PR re-confirmation; M2B.6 defect-fix flow (caught two defects, fixed inline) demonstrates self-review competence.
- **Proposed L1 change**: document the standing-approval pattern in `@release-manager` SKILL.md as an option (with hard gates around it: clear scope of authority, explicit standing approval, self-review checklist). This is essentially the autonomous-run plan pattern that this vertical exercised.
- **Project-local action**: none.

## L1 change proposals (handed off to `@update-horizontal` via next `@sprint-review`)

| Proposal | L1 artifact affected | Status |
|---|---|---|
| `/start-project` token substitution (sed-replace `your-pkg` + `your_pkg` at copy time per declared L3 token vocabulary) | `~/.codeium/windsurf/global_workflows/start-project.md` | proposed (M2B.3 entry; trigger satisfied per M2B.6) |
| `[[tool.mypy.overrides]]` pattern for conditional optional-dep imports | `~/.codeium/windsurf/skills/write-skill/SKILL.md` step 6 (or new `python-ml-uv-conventions.md` doc) | proposed (M2B.6 entry) |
| `@release-manager` separate-PR-per-artifact discipline clarification (single-PR-OK when artifacts live outside repo) | `~/.codeium/windsurf/skills/release-manager/SKILL.md` | proposed (M2B.4 entry; reinforced at M2B.5) |
| `@write-skill` step 6 line-count heuristic refinement (target body 100–130 lines for composing skills) | `~/.codeium/windsurf/skills/write-skill/SKILL.md` step 6 | proposed (M2B.4 calibration entry) |
| `@verify-l3-template` skill (run `make test` + `make lint` + `make typecheck` against L3 scaffolds) | new L1 skill at `~/.codeium/windsurf/skills/verify-l3-template/SKILL.md` | proposed (this retro §Learnings) |
| Standing-approval autonomous-run pattern documentation | `@release-manager` SKILL.md or `@begin` / `@kickoff` SKILL.md | proposed (this retro §Learnings) |

All five existing entries (3 + 2 new from this retro) live in `queue/pending-review.md` for `@sprint-review`'s drain.

## ADRs written this milestone

| ID | Title | Trigger |
|---|---|---|
| (none) | — | All decisions either fit the brainstorm B# answer or were resolved per ADR-011 supersession-over-deletion (placeholder refinement at M2B.6) without warranting a standalone ADR per the grill-with-docs 3-test |

The 3-test for the M2B.6 placeholder refinement: hard-to-reverse=No (the placeholder convention is documented in the scaffold's README + L3 skills' hard-gates; reversing means another sed-replace pass), surprising-without-context=Mild (the dual-token convention mirrors `scikit-learn`/`sklearn` real-world precedent), real-trade-off=No (PEP 503 + Python identifier requirements left no other valid path). 2/3 No → no ADR; commit body + queue entry suffice.

## Carry-forward to next milestone / sprint

- **M2B.8 — Cascade-D handoff** (next PR) — author `cascade-system/docs/handoffs/cascade-d-master-thesis.md` per handoff §3 M2B.8 spec; strict scope (no thesis-domain content). Closes Vertical B definitively.
- **`@sprint-review` drain** (Cascade A horizontal) — drain the 6 L1 proposals listed above. Two are high-leverage (`/start-project` token substitution; `@verify-l3-template` skill). The rest are documentation/calibration updates.
- **`python-ml-uv` consumer readiness** — template is `active` per cheat-sheet line 100. Cascade D (or any future Python ML / research project) can run `/start-project <name> python-ml-uv` immediately. The post-bootstrap rename step is documented in the scaffold's `README.md` "Post-bootstrap rename" section pending the L1 token-substitution enhancement.
- **Plan-files-per-vertical calibration** — Vertical B produced 2 plan files (kickoff orient + autonomous run); Vertical C produced 10. Big delta. Informs the `@kickoff --milestone <id>` mode question already queued from Vertical C's retro. Two data points; one more (Vertical D) closes calibration.

## Definition of done for this retro

- [x] All `## Friction / surprises` items have a learning or queue entry
- [x] All `## Learnings` items map to L1 proposals (3 listed) or are explicitly archived
- [x] L1 proposals are listed with their target artifact path
- [x] ADRs written this milestone are catalogued (none, with rationale)
- [x] Carry-forward list populated
- [x] Status changed to `closed`; `Date closed` filled
- [x] Working tree clean at retro-commit time (ADR-008 hard gate; this commit closes M2B.7 alone, no other diffs)

## Termination

Per handoff §8: Cascade D (thesis) can now run `/start-project <thesis-name> python-ml-uv` to bootstrap from this template. M2B.8 will provide the explicit Cascade-D handoff doc. L1 observations queued for Cascade A's next horizontal `@sprint-review`.

---

*Template source: `~/.windsurf/templates/_shared/retro-template.md`. Drained by `@sprint-review`. L1 proposals handed off to `@update-horizontal`.*
