# ADR-010: `/docs-refresh` flags empty subdirectories without rationale

**Status**: Accepted
**Date**: 2026-04-29
**Context**: Sprint 1 review (`docs/reviews/sprint-1-review.md` §3.3 m-1) observed two empty subdirectories in the cascade-system meta-repo: `docs/architecture/` and `docs/prompts/stages/`. Both are listed in `cascade-system/docs/structure.md` but neither is populated; both lack a `.gitkeep` or a README explaining the intent. The current `/docs-refresh` (M1.11) placement-validation table flags *missing required subdirs* but not *present-but-empty-without-rationale* subdirs, so the ambiguous state slipped past linting. Separately, the meta-repo's structure.md lists `docs/prompts/stages/` under "when this repo runs `phases.yaml`" — the repo does not run phases, so the directory is semantically wrong, not just empty.

**Decision**:

1. `/docs-refresh` Step 3 (Validate placement) gains a new violation row: **"Empty subdir without rationale"** — detected when a directory contains zero files AND the directory IS in the structure.md spec. Output a **warning**, not an error: "empty subdir — intentional placeholder (add `.gitkeep`) or orphan (remove from structure.md and disk)?". The skill does not auto-fix; user decides.
2. `cascade-system/docs/structure.md` is amended to remove `docs/prompts/stages/` (since the meta-repo does not run `phases.yaml`), and `docs/architecture/` is backfilled — either immediately (with a system-overview diagram) or left with a `.gitkeep` + README stub marking it as reserved for a future sprint. Recommendation: `.gitkeep` + README stub now; backfill the diagram in a future sprint.

**Alternatives considered**:

- **Hard-error on empty dirs** — rejected; empty-with-`.gitkeep` is a legitimate placeholder pattern.
- **Silently ignore empty dirs** — status quo. Rejected: that's what let the ambiguity persist.
- **Remove the dirs without amending structure.md** — rejected; structure.md is the spec, so the spec must change in lockstep.

**Consequences**:

- `/docs-refresh` gains a small lint that surfaces ambiguity without forcing a fix.
- The meta-repo's own `docs/` gets one-time cleanup (remove `prompts/stages/`, add stub to `architecture/`).
- Downstream projects using the `_shared/scaffold/` template already ship populated subdirectories (README + examples), so the lint will rarely fire for them — it mostly catches edge cases like the meta-repo's pre-scaffold bootstrap state.

**Source**: Sprint 1 review report `docs/reviews/sprint-1-review.md` §3.3 m-1 + §7 recommendation #4. Implementation lands in `~/.windsurf/skills/docs-refresh/SKILL.md` (new lint row in Step 3's table) + `cascade-system/docs/structure.md` (meta-repo-specific cleanup) in the same hardening batch.
