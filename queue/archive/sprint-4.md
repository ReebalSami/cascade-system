# Archive — Sprint 4 entries resolved during the horizontal prelude (M4A.x)

> Started 2026-09-06 at M4A.1 (ADR-037 apply, `ReebalSami/cascade-system#114`). Entries here were **RESOLVED** — the proposed L1 change landed in a PR — and are moved out of the live queue with a resolution note, per the queue-archive policy (`queue/archive/sprint-1-5.md` precedent). M4A.2 (ADR-038) appends the M2B.3/M2B.6 token-substitution entry here when it closes.

---

## RESOLVED — 2026-09-05 — Devin rule-loading semantics for `description`-only rule files

**Resolution date**: 2026-09-06 (M4A.1, PR for #114)
**Decision**: RESOLVED — clause landed exactly as proposed. `docs/rules/l1-canonical-paths.md` gained a "Loading facts that shape authoring" section ("every rule file declares `trigger:` explicitly — a `description`-only file is agent-decided in Devin and always-on in nothing"); `@verify-l1` step 6 checks `trigger:` presence on every `docs/rules/*.md`, `_shared/scaffold/.devin/rules/*.md`, and `<type>/rules/*.md`; `@propose-extension` Q5 and `/start-project` step 6a repeat the clause at authoring / deploy time. Concise `global_rules.md` unchanged (cap).
**Original entry text**:

- **Insight**: portfolio-website's `.devin/rules/*.md` files carry only `description:` (no `trigger:`). Devin lists them as "available rules — read when relevant" (agent-decided), not as always-on. So the same file behaves differently across tools depending on whether `trigger:` is explicit. Rule authors should always set `trigger:` explicitly; `description`-only is an accidental `model_decision`.
- **Source**: observed in the 2026-09-05 Devin session (`<available_rules>` block listing 19 portfolio rules by description); Devin docs `extensibility/rules.mdx` §"Rule Activation Types".
- **Proposed L1 change**: one clause in `docs/rules/l1-canonical-paths.md` (long-form) or in `@write-skill`/`@propose-extension` rule-authoring steps: *"every rule file declares `trigger:` explicitly; `description`-only files are treated as agent-decided by Devin and always-on by nothing."* Also a `@verify-l1` check for rule files without `trigger:`. Not a new rule (cap).
- **Trigger for promotion**: ADR-037 apply (M4A.1) is the natural moment since `@verify-l1` is touched anyway; otherwise next horizontal `@sprint-review`.

---

## RESOLVED — Sprint 3 — Cascade C2 — M_C2.3 author (`@write-skill` step 9 inaccuracy — missing `docs/skills/INDEX.md` update)

**Resolution date**: 2026-09-06 (M4A.1, PR for #114)
**Decision**: RESOLVED — `@write-skill` step 9 now reads "If L1: add a row to `docs/skills/INDEX.md` (activation + concise description + canonical path) AND bump the skill count + add a row in `docs/cheat-sheet.md` §1". Trigger fired as predicted: M4A.1 added ten L1 skills and had to perform exactly the index updates step 9 claimed did not exist. While updating, `docs/skills/INDEX.md` was found to be missing four further skills (`begin`, `kickoff`, `handoff-to-coding-session`, `handoff-to-thinking-session`) — also fixed. The `@update-horizontal` propagation-table sub-question (a row for "new L1 skill") is folded into step 9 itself rather than a separate row; `@docs-refresh` remains the regenerator.
**Original entry text**:

- **Insight**: Authoring `@vault-distill` at M_C2.3 surfaced a drift in `@write-skill` SKILL.md step 9 ("Update indexes / cross-refs"). The step currently reads: *"If L1: no global index file yet (M1.11 may add one); no action needed"*. This is inaccurate — `docs/skills/INDEX.md` exists since Sprint 1.5.4 (PR #35) and `docs/cheat-sheet.md` has a skill count that must be bumped when a new L1 skill lands. During M_C2.3 authoring, both indexes had to be updated manually; the SKILL.md gave no instruction to do so. Future skill-authoring sessions (B, D, and subsequent cascade-system skills) will hit the same friction unless the step is corrected.
- **Source**: ADR-031 §Consequences (future-work row); `~/.codeium/windsurf/skills/write-skill/SKILL.md` step 9 (lines 168–173); `docs/skills/INDEX.md` (exists since Sprint 1.5.4); `docs/cheat-sheet.md` §1 Skills count.
- **Proposed L1 change** (re-evaluate at M_C2.5 retro or on next invocation of `@write-skill`):
  - Update `@write-skill` SKILL.md step 9 to: *"If L1: update `docs/skills/INDEX.md` (add row for new skill with activation + concise description + canonical path) AND `docs/cheat-sheet.md` §1 (bump skill count + add row)"*. Route via `@update-horizontal` per ADR-017.
  - Also consider: does `@update-horizontal` step 8 Propagate table need a matching row for "new L1 skill" specifying the two index updates? Currently only "Skill description / behavior change" is listed — adding a skill isn't the same as modifying one.
- **Severity**: Low. Friction is noticeable (manual index bumps) but not blocking — human authoring caught it. Higher severity if an automated flow ever invokes `@write-skill` and relies on its step 9 being complete.
- **Trigger for promotion**: Next invocation of `@write-skill` for a new L1 skill (after M_C2.3); OR M_C2.5 retro; OR any future `@sprint-review` processes a queue of skill-authoring friction and this is one of them.
