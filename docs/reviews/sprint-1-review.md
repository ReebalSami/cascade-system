# Sprint 1 Review — L1 Foundation

**Reviewer**: Cascade A (horizontal, this conversation — the original Sprint-0 + planning Cascade)
**Reviewed**: Cascade A (Sprint 1 — fresh peer Cascade that executed M1.1 – M1.11)
**Date**: 2026-04-29
**Scope**: All Sprint 1 deliverables in `~/.windsurf/` and `~/Projects/cascade-system/docs/`
**Sprint retro**: `../../retros/sprint-1.md`
**ADR codifying this review subdir**: `../decisions/ADR-007-reviews-subdirectory.md`

---

## 1 — Summary

Sprint 1 (L1 Foundation) shipped a complete, internally-consistent system: 14 rules, 8 skills, 4 workflows, 1 contract, 2 shared templates, 12-file shared scaffold, 5 sprint-1 ADRs, 2 vertical-spawn handoffs, and a closed retro — all tracking GitHub issues #2 – #12.

The work is **architecturally sound, principle-faithful, and demonstrably end-to-end wired**. The concurrent ADR-004 numbering conflict was resolved cleanly with no lasting damage. Adapt-from-all and document-as-you-go discipline held throughout. All Sprint 1 GitHub issues closed.

There is **one critical operational gap**: the Sprint 1 work is not committed. Sprint 1 cannot be considered fully closed until that's resolved. Details under §3.

**Verdict**: Ready-to-commit (after fixing one operational gap; no architectural rework needed).

## 2 — Methodology

> **Note added 2026-05-01 (Sprint 1.5.6 drift fix)**: Path references in this section reflect pre-ADR-014 paths used during Sprint 1 (`~/.windsurf/skills/`, `~/.windsurf/workflows/`). The active L1 surface was migrated to `~/.codeium/windsurf/skills/` and `~/.codeium/windsurf/global_workflows/` per ADR-014 + ADR-016. Per ADR-011 (supersession over deletion), original paths are preserved here as historical record of what existed at Sprint 1 close. `~/.windsurf/contracts/` and `~/.windsurf/templates/` remain canonical per ADR-014.

I read every Sprint-1-shipped file end-to-end:

- 14 rules in `~/.windsurf/rules/` — full bodies *(historical — pre-ADR-014 path)*
- 8 SKILL.md in `~/.windsurf/skills/` — full bodies *(historical — pre-ADR-014 path)*
- 4 workflows in `~/.windsurf/workflows/` — full bodies *(historical — pre-ADR-014/016 path)*
- 1 contract `~/.windsurf/contracts/phase-taxonomy.md` — all 328 lines
- 2 templates + 12-file scaffold in `~/.windsurf/templates/_shared/`
- 7 ADRs (5 from Sprint 1 + 1 carried from Sprint 0 + ADR-007 authored during this review)
- 3 handoff prompts (Sprint 1 entry + B + C)
- Sprint 0 retro + Sprint 1 retro
- Pending-review queue
- `docs/structure.md` (cascade-system + downstream variant)
- GitHub state for issues #1 – #12 (all closed, verified via MCP)
- Git log + git status of `cascade-system` repo

Cross-references checked between every skill/workflow/rule and every consumer reader. No broken references found inside the L1 surface.

## 3 — Findings

### 3.1 — Critical

**C-1. Sprint 1 work is uncommitted in the `cascade-system` repo.**

The retro at `retros/sprint-1.md` line 4 declares `**Status**: closed`, but `git status` shows:

```
Modified (5):
  docs/decisions/INDEX.md
  docs/handoffs/INDEX.md
  docs/structure.md
  queue/pending-review.md
  retros/sprint-1.md

Untracked (4):
  docs/decisions/ADR-006-skill-frontmatter-schema.md
  docs/decisions/ADR-007-reviews-subdirectory.md       (this is mine, not Sprint-1 Cascade's)
  docs/handoffs/cascade-b-template-python-ml-uv.md
  docs/handoffs/cascade-c-obsidian.md
```

The Sprint 1 retro itself is uncommitted, the two Sprint-2 vertical handoffs are uncommitted, ADR-006 (the SKILL.md frontmatter schema — load-bearing for downstream Cascades B and D) is uncommitted, and the INDEX + structure updates are uncommitted.

The Cascade-B handoff at `cascade-b-template-python-ml-uv.md` line 107 lists `Final commit pushed to the cascade-system repo with a clean working tree` as an explicit definition-of-done item — yet the same Cascade authoring that handoff did not push its own definition-of-done.

This violates `make-sure-it-works` (verification before declaring complete) and weakly violates `be-honest-direct-critical` (claiming `Status: closed` when the work is not durably stored).

**Fix**: One commit with all 9 changes. Suggested message:

```
chore: close Sprint 1 — finalize retro, ADR-006, vertical-spawn handoffs

- ADR-006 (SKILL.md frontmatter schema, M1.2 outcome)
- cascade-b-template-python-ml-uv handoff (Sprint 2 Vertical B)
- cascade-c-obsidian handoff (Sprint 2 Vertical C)
- INDEX.md (decisions): ADR-005, ADR-006, ADR-007
- INDEX.md (handoffs): cascade-b, cascade-c
- structure.md: reviews/ subdir per ADR-007
- queue/pending-review.md: M1.1 deferrals appended by user
- retros/sprint-1.md: full sprint retro
```

ADR-007 (this review's subdir codification) and the review report itself are mine and can be in the same commit or a follow-up; they are review-cycle artifacts, not Sprint-1-Cascade's outputs.

### 3.2 — Major

**M-1. Concurrent-ADR-004 conflict resolution: clean, no lasting damage.**

Both Cascades authored ADR-004 simultaneously. The new Cascade renumbered theirs to ADR-005 and added a `Note on numbering` line; my ADR-004 retained its number with a matching concurrency note. Cross-checks:

- `docs/decisions/INDEX.md`: ADR-004 = scaffold pattern, ADR-005 = sprint-1 execution order. Consistent.
- ADR-004 (`shared-scaffold-pattern.md`) line 3: notes the concurrent authorship and matching note in ADR-005. ✓
- ADR-005 (`sprint-1-execution-order.md`) line 5: notes renumbering. ✓
- Plan was reverted to ADR-004 references for the scaffold pattern. Verified.
- No orphan ADR file (no leftover pre-renumber draft).
- Sprint 1 retro (`retros/sprint-1.md` line 9, 39, 67-70): the friction is documented honestly as a Sprint-1 process learning ("ADRs are claim-by-write — no allocation gate").

**This is a legitimate Major finding only because it deserves explicit acknowledgement that the system survived its first concurrency stress test cleanly.** No remediation needed. The retro already proposes a Sprint-2 evaluation: reserve numbers in INDEX.md before authoring vs accept post-hoc renumber. Defer to Sprint-2 `/sprint-review`.

**M-2. L1 components are entirely outside any version-controlled repository.**

All 14 rules, 8 SKILL.md files, 4 workflows, 1 contract, 2 shared templates, and the 12-file shared scaffold live in `~/.windsurf/` — which is not a git repo (verified — no `.git/` under `~/.windsurf/`).

This is a pre-existing systemic issue inherited from Sprint 0; plan §3.10 explicitly leaves the L1 ↔ repo linkage as TBD. Sprint 1's heavy authoring made the gap acute: a substantial body of carefully-authored L1 work has no commit history, no diff trail, no remote backup.

**Why this is Major (not Critical)**:
- Plan §3.10 acknowledges the gap, so Sprint 1 didn't violate plan-time intent.
- The cascade-system meta-repo *does* track the ADRs/handoffs/retros that document L1 decisions, so the *reasoning* is durable even if the *artifacts* aren't yet versioned.

**Fix (deferred)**: Sprint 2 or a dedicated bootstrap milestone should resolve this — likely by either (a) initializing `~/.windsurf/` as its own git repo with a remote, or (b) symlink/sync mechanism between `cascade-system/.windsurf/` and `~/.windsurf/`. Capture as a queue item.

### 3.3 — Minor

**m-1. Empty `docs/architecture/` and `docs/prompts/stages/` in cascade-system meta-repo.**

The `_shared/scaffold/` ships `architecture/` with a README + 2 example diagrams, but the cascade-system meta-repo predates `/start-project` and was never bootstrapped via the two-pass scaffold. Both directories exist (from M0.x raw scaffolding) and are empty.

For `docs/prompts/stages/`: per `cascade-system/docs/structure.md` rule 5, this is `(when this repo runs phases.yaml)`. The meta-repo does not run a phase chain. Recommend either (a) delete with a one-line ADR explaining "meta-repo doesn't run phases.yaml", or (b) leave empty with a `.gitkeep`. Currently it has neither — so it could be considered a placement-validation finding once `/docs-refresh` runs.

For `docs/architecture/`: there *is* legitimate content that could go here (system overview, cross-Cascade flow diagram). Recommend either backfilling during a future sprint or noting in queue.

Both are deferrable; not blocking Sprint 2.

**m-2. Sprint 1 retro M1.4 row says "9 files" for `_shared/scaffold/`; current count is 12.**

This is consistent if read as a snapshot at M1.4's close — M1.11 then added 3 architecture templates (README + 2 Mermaid examples per the M1.11 row) bringing the total to 12. The retro is per-milestone-snapshot, so the count is correct *at that milestone*. A future reader scanning only the M1.4 row could be momentarily confused.

Recommend a clarifier line on the M1.11 row already says "+3 architecture templates", which is sufficient. **No action needed**; documented for completeness.

**m-3. The Sprint 1 entry handoff (`cascade-a-sprint-1.md`) is marked "superseded — Sprint 1 closed" in `INDEX.md` but the file is retained.**

This matches `document-as-you-go` (supersession over deletion) and ADR conventions, but no formal rule yet says "retain superseded handoffs". Suggest the retention policy be made explicit in `docs/structure.md` or in a dedicated handoff-lifecycle ADR if future ambiguity arises. **Not a fix; an observation.**

**m-4. `start-project.md` step 8 wraps the initial-commit message in `git commit -m "..."` with embedded newlines.**

```
git -C <parent>/<name> commit -m "chore: bootstrap from L3 template <type> via /start-project

Two-pass scaffold:
- _shared/scaffold/ (universal docs structure, ...)
...
```

Per the user's saved memory on terminal multi-line commands, `git commit -m "..."` with multiple `-m` flags is preferred for multi-paragraph messages over a single embedded-newline `-m`. The current snippet works in `iTerm2`/`Ghostty` but is fragile in the Windsurf integrated terminal where multi-line strings can crash. The L1 workflow runs in *whichever* Cascade invokes `/start-project` — including the integrated terminal.

**Fix**: change the snippet to use multiple `-m` flags. Single-line in workflow doc, multi-paragraph in resulting commit. Trivial edit.

### 3.4 — Strong (positive observations to preserve)

**S-1. Adapt-from-all discipline was rigorous throughout.**

Every authored skill/rule/workflow/contract carries `sources_consulted` + `adapted_for` frontmatter pointing to specific upstream paths and licenses. Sample verifications:
- `grill-me/SKILL.md`: superpowers/skills/brainstorming + mattpocock/skills/productivity/grill-me, license-tagged, with `adapted_for` itemizing what was changed
- `phase-taxonomy.md` frontmatter: lists 4 upstream sources + `portfolio-website` (own) with explicit "no phase-set contract pattern" verdict — provenance even when nothing was adopted
- `docs-refresh/SKILL.md`: lists ADR-003/002/006 + structure.md + downstream variant + browsed superpowers/mattpocock — even sources that *weren't* adopted are cited

This level of provenance hygiene is unusual and should be preserved as a system-defining trait.

**S-2. Document-as-you-go was practiced in the moment, including self-criticism.**

ADR-002 (`docs/handoffs/` subdirectory) literally documents the new Cascade's own placement mistake and the user-flagged correction in plain language: `I (Cascade A) initially placed it at docs/handoff-cascade-a-sprint-1.md — docs/ root. User correctly flagged this as violating the structure prescribed in §3.10`. Fact-based self-criticism without melodrama. This is exactly what `be-honest-direct-critical` mandates.

**S-3. End-to-end flow is wired and traceable.**

Every L1 component references its consumers and producers consistently:
- Phase-taxonomy contract §5 enumerates 4 readers (`/run-phase`, `/recalibrate`, `/sprint-review`, `plan-drift-watcher`) and what each reads
- Each named reader's actual file confirms the contract's claims
- `/start-project` step 13 hands off to `/run-phase`
- `/grill-me` produces `02-brainstorm.md` consumed by `/to-prd`
- `/to-prd` §11 produces vertical slices consumed by `/to-issues`
- `/sprint-review` drains `queue/pending-review.md` populated by `bidirectional-learning-pipe` and proposes via `/update-horizontal`
- `plan-drift-watcher` (passive) suggests `/recalibrate` (active)
- `sprint-review-prompt` (passive) suggests `/sprint-review` (active)

No dangling references found. Forward-references (e.g., `/sprint-review`'s mention of `/update-horizontal` before M1.10) are docs-only, not runtime-resolved, which the retro correctly notes.

**S-4. Hard-gate pattern consistent across all state-mutating skills.**

Every skill that writes state (`/start-project`, `/to-prd`, `/to-issues`, `/sync-github`, `/sprint-review`, `/write-skill`, `/update-horizontal`, `/add-project-type`, `/docs-refresh`) declares an explicit "do not act until user approves" gate. Verified by reading each.

**S-5. No-time-estimates and no-quantity-over-shape are honored throughout.**

Surveyed every Sprint-1-shipped doc for time language ("days", "weeks", "soon", "by Friday") and fixed-count prescriptions ("we need N skills", "build M rules"): none found in plans or design content. The retro's "shape" descriptions (e.g., "150–300 line range" for skill length) are observational, not prescriptive — that's the intended use of numbers (describing what shipped, not prescribing what *must* ship).

**S-6. Sprint-1 execution order ADR (ADR-005) demonstrates principle-following over numerical convenience.**

The choice to author M1.5 before M1.4 — explicitly because numbering "is a side-effect of creation order ... not an architectural contract" (`no-quantity-over-shape`) and dependencies decide order — is the kind of disciplined trade-off the system was designed for. The ADR captures the user's own reasoning verbatim, which both honors `document-as-you-go` and gives future Cascades a clear precedent.

**S-7. Bidirectional learning pipe drained correctly and conservatively.**

`queue/pending-review.md` retains the M1.1 deferrals (`skill-by-skill`, `local-demo-before-push`, `config-yaml-first`, `github-project-management`) for re-evaluation. The retro stages 3 new observations (skill length heuristic, multi-Cascade ADR-numbering coordination, disk-headroom check) as "observed; not yet promoted" — deferring promotion to Sprint-2's `/sprint-review` because that's when `/update-horizontal` gets its first real cross-vertical evidence. This is the correct conservative posture per the rule's design.

**S-8. GitHub state matches retro state.**

All 12 issues (#1 from Sprint 0 M0.6, #2 – #12 for Sprint 1 milestones) are `CLOSED`. Zero open issues. Project board state reconcilable from MCP read.

**S-9. Two Sprint-2 handoffs (`cascade-b-template-python-ml-uv.md`, `cascade-c-obsidian.md`) are exemplary.**

Each is self-contained, includes ordered read list with rationale, scope guards ("you are vertical, not horizontal"), pitfalls drawn from Sprint-1 friction, definition of done, and termination message. The Cascade-B handoff explicitly references ADR-004's `_shared/scaffold/` interaction as **Critical** reading — which is exactly the cross-Cascade transfer of mid-sprint design that the handoff protocol was designed to test.

## 4 — Concurrency-damage assessment

The core question: did simultaneous Cascade-A modifications cause lasting damage?

**Answer: No.**

Three concurrent surfaces existed:
1. **ADR numbering** — both Cascades claimed ADR-004 simultaneously. Resolution by post-hoc renumber + matching `Note on numbering` lines. INDEX.md is consistent. Plan reverted cleanly. No orphaned files. **Clean.**
2. **Plan amendments** — I authored §3.10 + §4 M2B.3 + §5 file-tree changes for the scaffold pattern; new Cascade A consumed those during M1.4. Plan was reverted to ADR-004 numbering throughout. Verified by reading the plan file. **Clean.**
3. **Docs-structure** — I added `reviews/` to `cascade-system/docs/structure.md` (during this review session, after Sprint 1 closed). The new Cascade A added `handoffs/` and consequences earlier. Both edits are non-overlapping in structure.md content. **Clean.**

No file was lost, no edit was overwritten without a documented decision, no ADR has stale numbering, no plan section conflicts. The system's "ADRs as the durable record + INDEX as the reconciliation point" pattern held under the first real concurrency stress.

## 5 — Reachability check

The new Cascade had access to: `~/.windsurf/`, `~/Projects/cascade-system/`, `~/Projects/portfolio-website/.windsurf/` (via `refs/`), and `~/Projects/cascade-system/refs/` (vendored upstreams).

Reachability assessment:
- **L1 surface (`~/.windsurf/`)**: full read/write, used extensively. ✓
- **Meta-repo (`cascade-system/`)**: full read/write of docs + retros + queue. ✓
- **Vendored refs (`refs/`)**: read-only access used per `adapt-from-all`. Citations show specific paths consulted. ✓
- **Plan (`~/.windsurf/plans/cascade-project-system-cac5f9.md`)**: read + amendment as needed. ✓
- **Portfolio-website rules (`~/Projects/portfolio-website/.windsurf/`)**: M1.1 extraction successful for 7 rules. ✓
- **GitHub state**: MCP reads + writes (issue closes, comments) successful. ✓
- **`gh` CLI** (token bypass): not exercised post-bootstrap (no Project mutations needed in Sprint 1; project + issues already existed from Sprint 0).

**Workspace scope was sufficient.** No "I don't have access to X" friction reported in the retro. The single operational gap (no commit) is unrelated to scope — `git` was available; the closure step was simply skipped.

## 6 — Adherence to principle rules

Each of the four originating principle rules (`no-time-estimates`, `no-quantity-over-shape`, `adapt-from-all`, `document-as-you-go`) was held:

| Rule | Evidence |
|---|---|
| `no-time-estimates` | All Sprint-1 docs phrase progress in event-based language ("after the smoke test passes", "when the milestone closes"). No calendar dates as deadlines. Retro uses event-based status. |
| `no-quantity-over-shape` | ADR-005 explicitly invokes the rule when justifying M1.5-before-M1.4 reorder. Plans + ADRs describe shape (archetypes, dependencies) over counts. The retro discusses "shape" of skill lengths rather than prescribing a number. |
| `adapt-from-all` | Verified frontmatter on every authored skill/rule/workflow/contract. Even non-adopted browses are cited. |
| `document-as-you-go` | 5 Sprint-1 ADRs landed in real time; ADR-002 captures a self-correction in the moment. ADR-006 closes a plan-§11 open question at the moment the question came up. |

The 10 promoted rules from M1.1 (anti-laziness, no-half-knowledge, context7-and-docs-first, make-sure-it-works, no-terminal-oneline-scripts, be-honest-direct-critical, clean-project-structure, plus the 4 originals) are all in effect during the same sprint that authored them — verified by checking that `make-sure-it-works`'s "evidence over claims" mandate is the lens that surfaces finding C-1 above.

## 7 — Recommendations to the user (priority-ordered)

1. **(C-1)** Commit the 9 outstanding files to the `cascade-system` repo before declaring Sprint 1 closed. One commit, one push.
2. **(M-1)** Defer to Sprint 2 review.
3. **(M-2)** Add a queue entry: "Decide L1 ↔ repo linkage strategy for `~/.windsurf/` (git init? symlink?)". Tag for Sprint 2 or a dedicated meta-bootstrap micro-sprint.
4. **(m-1)** Either backfill `docs/architecture/` with a system-overview diagram (queue item) or accept empty until needed.
5. **(m-4)** When `/start-project`'s commit-message snippet is exercised for the first time, refactor to multiple `-m` flags. Tracked as a queue item — no need to fix preemptively.
6. **(m-3)** Consider an addendum to `docs/structure.md` clarifying retention policy for superseded handoffs. Low priority.

Findings m-2 and m-3 require no action.

The sprint as a whole is **strong work**. I would recommend proceeding to Sprint 2 (vertical spawn) immediately after finding C-1 is resolved.

## 8 — What this review tested in the system itself

This review is the first instance of the horizontal-Cascade-reviews-the-vertical-Cascade pattern. ADR-007 (authored during this session) codifies `docs/reviews/` as the canonical home. Future trigger events:

- Sprint close (every sprint after this one)
- Vertical close (e.g., when Cascade B finishes `python-ml-uv` template)
- Mid-sprint when verticals report stalls or completion

The fact that this review itself surfaced both a critical operational gap (uncommitted work) **and** confirmed multiple positive system traits (concurrency-damage-free, principle-faithful, end-to-end-wired) suggests the review pattern has signal density and is worth keeping as a durable Sprint-boundary ritual.

---

*Review report authored by Cascade A (horizontal/mentor mode). Sources: every file listed in §2. Cross-references checked manually — no automated validator yet (M1.11's `/docs-refresh` validates docs placement only, not cross-referential integrity).*
