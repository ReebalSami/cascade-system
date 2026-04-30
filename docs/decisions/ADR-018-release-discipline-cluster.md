# ADR-018: Release-discipline cluster

**Status**: Accepted
**Date**: 2026-04-30
**Supersedes**: none (extends ADR-013's `/commit` forcing-function pattern from commit-shape to branch+PR shape)
**Source**: `docs/architecture/parked-items-brainstorm.md` §1–§4 + §7 + §8 (Sprint 2 prep brainstorm; build-now approval). Original design: `~/.windsurf/plans/post-sprint-1-hardening-batch-2-cac5f9.md` (367 lines, 2026-04-29; path-stale per ADR-014/016).

## Context

Sprint 1 hardening shipped directly to `main` without a branch or PR despite git-flow being "obvious". Same failure shape as the embedded-newline-`-m` violation that ADR-013 fixed via `/commit`: rules-as-text are insufficient when the unsafe path is the path of least resistance.

`main` integrity was being maintained by:
- vibes ("don't push to `main`")
- `git status --porcelain` clean-tree gate at sprint close (ADR-008) — *post-hoc*, not preventive
- nothing else

There was no preventive forcing function. This ADR codifies one.

A second concern surfaced in the brainstorm: the user runs M1 Pro / 16 GB / Metal / no CUDA. ML-flavored tasks in Sprint 2+ (Cascade D thesis work) will exceed local capacity. Without an agent-side hardware-awareness rule, the agent could spin up AWS resources without surfacing cost. This ADR also codifies the AWS escalation policy as a workspace-deployed rule.

## Decision

Build a **release-discipline cluster** consisting of:

| # | Artifact | Path | Type |
|---|---|---|---|
| 1 | `branch-and-pr-required` | `docs/rules/branch-and-pr-required.md` (long-form) + `~/.codeium/windsurf/memories/global_rules.md` (concise) | Rule, L1 always_on |
| 2 | `know-your-hardware` | `docs/rules/know-your-hardware.md` (long-form) → deployed to `<project>/.windsurf/rules/` by `/start-project` step 6a | Rule, L2 model_decision (workspace-only — see brainstorm drift correction below) |
| 3 | `@release-manager` | `~/.codeium/windsurf/skills/release-manager/SKILL.md` | Skill, L1 (auto activation) |
| 4 | `/branch-start` | `~/.codeium/windsurf/global_workflows/branch-start.md` | Workflow, L1 |
| 5 | `/branch-push-and-pr` | `~/.codeium/windsurf/global_workflows/branch-push-and-pr.md` | Workflow, L1 |
| 6 | `/ci-watch` | `~/.codeium/windsurf/global_workflows/ci-watch.md` | Workflow, L1 |
| 7 | `/branch-merge-and-cleanup` | `~/.codeium/windsurf/global_workflows/branch-merge-and-cleanup.md` | Workflow, L1 |

The cluster mirrors ADR-013's pattern: a **rule** (`branch-and-pr-required`) names the invariant; a **skill** (`@release-manager`) owns the orchestration; **workflows** (`/branch-*`) handle deterministic steps; the **rule** points at the skill so the agent has a forcing function whenever it considers `git push` to `main`.

### Brainstorm drift correction

The brainstorm `docs/architecture/parked-items-brainstorm.md` §2.3 + §3.3 specified:

- §2.3: `know-your-hardware` would have a section in `global_rules.md` with `(model_decision)` annotation
- §3.3: activation would be `model_decision`

Both are wrong per Windsurf docs (`https://docs.windsurf.com/windsurf/cascade/memories`):

> "The global rules file (`global_rules.md`) and root-level `AGENTS.md` files don't use frontmatter — they are always on."

`global_rules.md` is **always-on** with no per-rule activation modes. The `(model_decision)` annotations currently in `global_rules.md` (e.g., `## context7-and-docs-first (model_decision)`) are decorative — the whole file is loaded into the system prompt on every message. Real `model_decision` activation only works for **workspace rules** (`<project>/.windsurf/rules/<name>.md` with `trigger: model_decision` frontmatter).

**Corrected architecture for `know-your-hardware`**: long-form archive at `docs/rules/know-your-hardware.md` with `trigger: model_decision`, deployed to every project's `.windsurf/rules/` by `/start-project` step 6a (the per-project copy mechanism ADR-014 already established). **No `global_rules.md` entry.** Edge case: brand-new projects that haven't run `/start-project` yet won't have the rule loaded; acceptable since brand-new projects rarely run heavy compute on day 0.

This correction also resolves brainstorm §8 Q4 without trimming any existing rule. `branch-and-pr-required` concise body fits in `global_rules.md` at ~165 chars (5822 + 165 = 5987 / 6000).

### Skill-vs-workflow choice for `@release-manager`

Per ADR-015: skills are stateful, judgment-heavy, multi-step; workflows are deterministic procedures. The push → PR → CI → merge → cleanup flow has multiple decision points (CI failed? PR description ready? branch deletable?). Only a skill can carry that judgment. Helpers (`/branch-*`) stay as workflows because each is a deterministic procedure that is also independently invocable.

Alternative considered: workflow-only (no central skill, just `/branch-*` commands). Rejected — no central agent owns the invariants ("`main` is never pushed directly"; "no zombie branches"); same rules-as-text failure mode that birthed this ADR.

Alternative considered: rule-only with `/commit`-style forcing function (no skill, no workflows). Rejected — a rule alone is what *failed* in Sprint 1 hardening; the lesson is "need an active orchestrator", not "stronger rule".

### In-place branch vs worktree default

`@release-manager` defaults to `git checkout -b <type>/<topic-slug>` (in-place). `--worktree` is opt-in via `/branch-start --worktree`.

Rejected: worktree-default. Sprint 2 has parallel verticals B + C, but they run in **separate Cascade sessions** in **separate concerns** — not parallel branches in one repo. Worktree-default would complicate the mental model for the common case.

### Cold-start exception

`branch-and-pr-required` allows `git push -u origin main` immediately after `gh repo create` when **all three** hold:

1. Branch has no upstream (`git rev-parse --abbrev-ref --symbolic-full-name @{u}` fails)
2. Working tree contains exactly 1 commit ahead of empty
3. Remote has no `main` branch yet (`gh api repos/:owner/:repo/branches/main` returns 404)

State-based detection (not time-based) per `no-time-estimates`.

### Squash-merge default

`/branch-merge-and-cleanup` defaults to `gh pr merge --squash --delete-branch <pr>`. Rationale: linear `main` history matches solo-dev workflow; multi-commit PR descriptions get preserved in squash body. User can override per-PR.

### Branch-protection on `main`

`/start-project` step 11 (a new step added by this ADR, between push and Project v2 creation) calls:

```sh
gh api -X PUT repos/:owner/:repo/branches/main/protection \
  -f required_pull_request_reviews.required_approving_review_count=1 \
  -f enforce_admins=false
```

Single-line `gh api` invocation (per `no-terminal-oneline-scripts`). On HTTP 403 (free-tier private repo cannot enforce protection), skill skips with a warning. On public repos and Pro+ private repos, protection enforces.

### Sprint 2 amendment scope (M2A.1–M2A.6)

Per brainstorm §7.2, this ADR is a **mid-Sprint-2 amendment** (precedent: ADR-004 was a mid-Sprint-1 amendment that landed cleanly). The cluster becomes Vertical A (Cascade A's own work alongside Verticals B and C):

- **M2A.1**: `branch-and-pr-required` rule
- **M2A.2**: `know-your-hardware` rule
- **M2A.3**: `@release-manager` skill
- **M2A.4**: 4 helper workflows
- **M2A.5**: `manual.md` mental-model + vertical-spawning sections (Phase B of build plan)
- **M2A.6**: `manual.md` full-release-discipline section (Phase B of build plan)

## Alternatives considered

- **Defer to Sprint 3**: rejected. The push-to-`main` pattern was already exercised in Sprint 1 + the unpushed-to-origin batch; one more sprint of vibes-discipline risks normalizing it. Mid-sprint amendment via ADR is the proven pattern (ADR-004).

- **Trim existing global rules to fit both new rules in `global_rules.md`**: rejected per user direction (`@/Users/reebal/.windsurf/plans/build-parked-items-6984b3.md` §"Q4"). The architectural fix (workspace-only deploy for `know-your-hardware`) is cleaner and honors the no-trim principle.

- **Combine `release-manager` and `commit` skills**: rejected. `/commit` is a workflow (deterministic file-based commit). `@release-manager` is a skill (stateful orchestration). Different shapes per ADR-015.

- **Skip the helper workflows; inline procedures into the skill body**: rejected. Skill body would exceed the 150-line norm (per ADR-006 + mattpocock heuristic); helpers also need to be independently invocable (e.g., `/branch-start` for a quick branch without going through the full release flow).

## Consequences

**Enables:**

- `main` cannot be pushed to directly (always-on rule fires whenever `git push` is contemplated outside `@release-manager`)
- AWS escalation requires explicit user approval with budget surfaced (workspace rule fires on resource-heavy intent)
- Release flow is auditable: every change to `main` arrives via squash-merged PR
- Skill body (≤150 lines) + 4 helper workflows compose; helpers reusable independently
- Branch-protection setup is automated for new projects (with graceful degradation on free-tier private repos)

**Constrains:**

- Solo-dev workflow now requires a PR even for trivial changes. Mitigation: PR description auto-fills from commit; user-review hard-gate is fast.
- `@release-manager` cannot run on cold-start (chicken-and-egg — first commit + push of a new repo). Cold-start exception in `branch-and-pr-required` covers this.
- `/start-project` adds branch-protection step 11; failures (e.g., 403) require user attention but do not block project creation.
- `know-your-hardware` is invisible on brand-new projects until `/start-project` runs. Acceptable; brand-new projects rarely train models on day 0.

**Self-test plan:**

The cluster's first real exercise is **the build of itself**:

- **Phase A** (this commit): branch `chore/release-discipline-foundation` → manual PR → squash-merge. Manual because the skill that would orchestrate doesn't exist yet (chicken-and-egg).
- **Phase B**: `manual.md` deferred sections, authored using the newly-existing `@release-manager`. Real dogfood.

If `@release-manager` fails to orchestrate Phase B, that's a Phase A bug; capture inline to `queue/pending-review.md` for Sprint 2 retro.

**Empirical-test footnote (per ADR-017)**: this ADR + cluster were synthesized via `parked-items-brainstorm.md` (which performed `@propose-extension` steps 1–8 in batched form per user choice during plan-mode). The dispatch step (step 9) executes via this Phase A commit. If a future Cascade authors comparable artifacts *without* engaging `@propose-extension`'s logic (route table, 3-test, `adapt-from-all`), that's the discoverability bug ADR-017 anticipates; capture to queue.

**Empirical-test stanza (own discoverability)**: `@release-manager` should auto-fire when a user message contains any of the `When to use` trigger phrases enumerated in the skill body (`ship`, `merge`, `land`, `PR`, `release`, `push to main`). The first vertical Cascade session that wants to land a change on `main` is the live-fire test. If a fresh Cascade reaches a `git push origin main` intent *without* having engaged `@release-manager` (no branch, no PR), that's a discoverability bug worth capturing to queue — the `branch-and-pr-required` always_on rule should backstop it, but skill auto-activation on description-match is the preferred forcing function. Note: Cascade auto-activation matches on user phrasing, not on intent introspection of the agent's own queued tool calls; the rule is therefore the load-bearing backstop, the skill is the orchestrator. Mirrors ADR-017's empirical test for `@propose-extension`. Cross-reference: `~/.windsurf/plans/three-question-audit-475e93.md` Q1+Q3 surfaced the need for this stanza.

## Verification

Acceptance criteria for Phase A:

1. `~/.codeium/windsurf/skills/release-manager/SKILL.md` exists, ADR-006 frontmatter complete, body ≤ 150 lines
2. 4 helper workflows exist under `~/.codeium/windsurf/global_workflows/`, each with `description:` frontmatter
3. `~/Projects/cascade-system/docs/rules/{branch-and-pr-required,know-your-hardware}.md` exist with full frontmatter
4. `docs/rules/INDEX.md` lists both new rules
5. `~/.codeium/windsurf/memories/global_rules.md` ≤ 6,000 chars (verified via `wc -m`); contains `## branch-and-pr-required` section; **does not** contain `## know-your-hardware` section (per drift correction); `:10` drift fixed (`workflows/` → `global_workflows/`)
6. `queue/pending-review.md` has the brainstorm drift footnote for Sprint 2 retro
7. No path leaks to `~/.windsurf/{skills,workflows,rules}/` (per ADR-014)
8. Phase A merged to `main` via squash-merge of PR (not direct push)

Phase B verification (separate commit):

9. `manual.md` has 3 new sections (mental model, vertical-spawning, full release discipline)
10. Phase B was authored via `@release-manager` (the skill from Phase A, not manual branching) — proves the dogfood

## Source refs

- Brainstorm: `docs/architecture/parked-items-brainstorm.md` (557 lines, 2026-04-30, commit `fa48ea2`)
- Plan: `~/.windsurf/plans/build-parked-items-6984b3.md`
- Original design: `~/.windsurf/plans/post-sprint-1-hardening-batch-2-cac5f9.md` (path-stale; superseded by brainstorm)
- Pattern precedent: ADR-013 (`/commit` as forcing function for commit-shape; this ADR extends to branch+PR shape)
- Path discipline: ADR-014 (L1 paths) + ADR-016 (`global_workflows/` correction)
- Invocation syntax: ADR-015 (`@` skills vs `/` workflows)
- Routing: ADR-017 (`@propose-extension` intake channel; this ADR is its first dispatched batch)
- Numbering: ADR-009 (NNN reserved in INDEX before authoring this file)
- Adapt-from-all: `refs/superpowers/skills/{finishing-a-development-branch,using-git-worktrees,requesting-code-review}/SKILL.md` (adopted patterns); `refs/claude-skills/engineering/release-manager/SKILL.md` (browse-only — semver/changelog scope mismatch)
