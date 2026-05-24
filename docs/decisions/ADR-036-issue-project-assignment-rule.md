# ADR-036: `issue-project-assignment-required` rule + `/issue-create` workflow

**Status**: Accepted

**Context**:

During the HORUS evidence-base audit follow-up (Cascade D, 2026-05-23), three issues (`ReebalSami/horus#62`, `#63`, `#64`) were filed via bare `gh issue create` calls. The user flagged that none of the three appeared on the `horus roadmap` Project v2 board — they had been created on the repo but never added to the Project, sitting as orphans invisible to planning views, `@sync-github`, and milestone triage.

User-stated complaint (verbatim):

> "why there are issues that are not assigned to a project?!! never create an issue without assingning to the project. Make sure this doesnt happen in the future. so please add this to the suitable rule, workflow etc."

The gap is structurally identical to the class of high-blast-radius invariants already addressed by:

- `no-terminal-oneline-scripts` rule (always-on, paired with `/commit` workflow — ADR-013)
- `branch-and-pr-required` rule (always-on, paired with `@release-manager` skill + 4 helper workflows — ADR-018)

Both prior cases established the precedent that **rules-as-text alone are insufficient for invariants where the violation is silent and the agent can self-correct only by remembering the rule on every relevant tool call**. The forcing-function workflow makes the safe path the only path.

This ADR codifies the same pattern for project-board hygiene.

**Decision**:

Adopt a four-artifact cluster:

1. **`issue-project-assignment-required` long-form rule** at `cascade-system/docs/rules/issue-project-assignment-required.md` (frontmatter `trigger: always_on`). Full pre-flight checklist, banned/allowed patterns, forcing-function pairing, rationale, interactions, consequences.

2. **Concise section in `~/.codeium/windsurf/memories/global_rules.md`** (~500 chars, always-on, the LAW). Sits after `branch-and-pr-required` (sibling discipline rule). Stays under the 6000-char cap via surgical compression of `l1-canonical-paths`, `bidirectional-learning-pipe`, `make-sure-it-works` (artifact-review paragraph merge), and the preamble.

3. **`/issue-create` workflow** at `~/.codeium/windsurf/global_workflows/issue-create.md`. The forcing function. Steps: resolve repo → check `gh project list` → invoke `gh issue create --project "<title>"` if Project exists OR plain `gh issue create` if not → verify via `gh project item-list` → surface URLs.

4. **Workspace propagation** of the long-form rule to active projects' `.windsurf/rules/`. Initial deployment to HORUS via a separate PR on `ReebalSami/horus` (since `branch-and-pr-required` forbids cross-repo bulk commits). Future projects pick it up automatically via `/start-project` step 6a once that workflow's rule-copy loop catches the new file.

**Alternatives considered**:

- **Rule-only (no workflow)** — relies on the agent self-disciplining on every `gh issue create`. Rejected: the violation that triggered this ADR happened despite the agent (Cascade D) knowing the value of project tracking — proves rules-as-text alone don't suffice for this class. Same lesson as ADR-013 (commit body bodies still got embedded-newlined despite the rule existing) and ADR-018 (push-to-main happened in Sprint 1 despite git-flow being "obvious").
- **Server-side enforcement via GitHub branch protection / required workflows** — `gh issue create` is not gated by repo policy on GitHub's side (no equivalent of branch-protection for issue creation). Not technically feasible.
- **Modify `@sync-github` to refuse-and-re-prompt on detected orphans** — would catch orphans retroactively but leaves the discipline gap open at issue-create time. Use `@sync-github` as the BACKSTOP, not the primary gate.
- **Workspace-only rule (not in `global_rules.md`)** — would mean the rule only fires when Cascade activates with a project workspace open. Rejected: `gh issue create` can run from any working directory, and Cascade sometimes files issues on the cascade-system repo itself or on cross-repo work. Always-on is the right surface.
- **Skip the `/issue-create` workflow; just edit the rule** — rejected per the rule+forcing-function precedent. The workflow is small (≤80 lines) and self-contained; the cost is low and the discipline value is high.

**Consequences**:

- All future `gh issue create` calls in Cascade-driven sessions route through `/issue-create` OR explicitly include `--project "<title>"`. The agent self-applies the pre-flight check on every issue creation.
- The 6000-char cap on `global_rules.md` is preserved via compression. The compressed sections retain semantic completeness — the long-form versions in `cascade-system/docs/rules/` remain unchanged and authoritative.
- HORUS gets the propagated rule via a follow-up PR. Other active projects (Portfolio Website, Recipes App, OTTO, Fraud detection, Cascade System itself) inherit on next `/start-project`-driven refresh or on demand if they have active Cascade work.
- `@sync-github` continues to function as the backstop reconciler. No behavior change to its existing flow; the new rule reduces the orphan rate it has to remediate.
- This ADR is the canonical statement for project-board hygiene. Future drift in this area supersedes via a new ADR per the standard `document-as-you-go` retention policy.
- The empirical-promotion path (user-flagged gap → direct L1 promotion without `queue/pending-review.md` intermediate) is a valid acceleration when the user explicitly initiates the change. The standard path (capture → drain → promote) remains the default; this case is the exception.

**Source**:

- HORUS evidence-base audit follow-up conversation, Cascade D, 2026-05-23
- `~/.windsurf/plans/audit-branch-disposition-14db9b.md` — the parent plan (Step 1 dispatched + Kickoff 1 complete; Step 2 is THIS ADR's promotion)
- `ReebalSami/horus#62`, `#63`, `#64` — the three issues that triggered the gap detection; all retroactively added to `horus roadmap` Project v2 board (project number 6) via `gh project item-add` before this ADR was authored
- ADR-013 (commit-workflow forcing function) — pattern precedent #1
- ADR-018 (release-discipline cluster) — pattern precedent #2
- `~/.codeium/windsurf/skills/sync-github/SKILL.md` — the paired retroactive reconciler
