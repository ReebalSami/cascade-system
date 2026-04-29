# ADR-008: `/sprint-review` requires clean working tree before declaring retro closed

**Status**: Accepted
**Date**: 2026-04-29
**Context**: Sprint 1 review (`docs/reviews/sprint-1-review.md` §3.1 C-1) surfaced that the Sprint 1 retro was marked `Status: closed` while 9 files (the retro itself, both Sprint 2 handoffs, ADR-006, INDEX + structure updates) were uncommitted in the `cascade-system` repo. The heartbeat skill `/sprint-review` (M1.9) had no step that verified git-clean state before allowing closure, so the durability requirement implicit in `make-sure-it-works` was not enforced.

**Decision**: `/sprint-review` gains a hard-gate step that runs `git status --porcelain` across all touched repos (meta-repo for horizontal sprints; `<project>/` for verticals) before the retro can be marked `Status: closed`. If dirty: produce a commit manifest, propose a single commit, block closure until the user approves the commit + push or explicitly acknowledges the dirty state as intentional (rare — must be justified in the retro body).

**Alternatives considered**:

- **A new always-on rule `commit-before-sprint-close`** — rejected as too narrow; rules are for broad invariants, not workflow-step enforcement. The skill is the right home because it's already the procedural owner of sprint closure.
- **Amend `make-sure-it-works`** — rejected; that rule is about "verify your code runs / your tests pass", a different kind of verification. Conflating durability-verification with functional-verification dilutes both.
- **Defer to user discipline** — rejected per the review finding: discipline alone failed on Sprint 1's first cycle. A structural gate costs nothing and catches the class of error.

**Consequences**:

- Every future `/sprint-review` invocation verifies durability before declaring closure.
- Retros with `Status: closed` have a stronger invariant: the work is in-repo, not just on-disk.
- Escape hatch (explicit-ack) preserves flexibility for intentional dirty states (mid-investigation, pending-peer-review, etc.) but makes them conscious.
- When M-2 resolves (L1 storage strategy), the gate also covers `~/.windsurf/`; until then, the skill skips that check with a clear warning.

**Source**: Sprint 1 review report `docs/reviews/sprint-1-review.md` §3.1 C-1 + §7 recommendation #1. Implementation lands in `~/.windsurf/skills/sprint-review/SKILL.md` in the same hardening batch.
