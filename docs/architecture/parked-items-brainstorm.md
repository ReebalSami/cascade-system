# Parked-items brainstorm — Sprint 2 prep

**Status**: Draft
**Date**: 2026-04-30
**Phase**: brainstorm (meta-repo; cascade-system has no `phases.yaml` — tracked here per `docs/structure.md` §6)
**Empirical test of**: ADR-017 (`@propose-extension` intake channel) — see §7
**Scope**: 3 named items + tightly adjacent (helper workflows, manual deferred sections); rest is triaged in §6
**Synthesis mode**: grill-me discipline applied in one pass — recommendation + 2–3 alternatives per branch, no Option A/B menus

---

## 0 — Background

### What's parked

Per `~/.windsurf/plans/post-sprint-1-hardening-batch-2-cac5f9.md` (367-line design doc authored 2026-04-29 then **not built**), `queue/pending-review.md`, and `retros/sprint-1.md`:

| Cluster | Items | Source |
|---|---|---|
| Release discipline | `release-manager` skill + `branch-and-pr-required` rule + 4 helper workflows | `post-sprint-1-hardening-batch-2-cac5f9.md` §1 |
| Hardware-aware tool selection | `know-your-hardware` rule + AWS escalation policy | `post-sprint-1-hardening-batch-2-cac5f9.md` §2 |
| Manual gaps | mental model overview + vertical-spawning conventions + full release discipline section | `docs/manual.md:93-95` (deferred), `post-sprint-1-hardening-batch-2-cac5f9.md` §3.4 |
| Architecture diagrams | `system-overview.mmd`, `cross-cascade-flow.mmd`, `phase-taxonomy.excalidraw` | `docs/architecture/README.md:7-11` |
| Meta-repo CI | markdown lint + ADR INDEX consistency + doc placement + link integrity | `post-sprint-1-hardening-batch-2-cac5f9.md` §3.6 |
| L1 storage strategy (M-2) | dotwindsurf repo / cascade-system/l1/symlink / sync script | `queue/pending-review.md` Sprint 1 review M-2 |
| Sprint 1 deferred rule promotions | `skill-by-skill`, `local-demo-before-push`, `config-yaml-first`, `github-project-management` | `queue/pending-review.md` Sprint 1 M1.1 |
| Sprint 1 observed-not-promoted | SKILL.md length heuristic, multi-Cascade ADR numbering, disk-headroom, queue archive policy | `retros/sprint-1.md` §"L1 change proposals" |

### What's changed since the original design

The `post-sprint-1-hardening-batch-2-cac5f9.md` plan predates 4 ADRs and the `@propose-extension` skill:

- **ADR-014** (L1 canonical storage paths, 2026-04-30) — moved skills/rules/workflows from `~/.windsurf/` to `~/.codeium/windsurf/` (the original plan's path references are stale)
- **ADR-016** (workflow path correction, 2026-04-30) — workflows live under `~/.codeium/windsurf/global_workflows/`, not `workflows/`
- **ADR-017** (`@propose-extension` as single intake channel, 2026-04-30) — every new artifact must route through this skill; the empirical-test clause names this brainstorm explicitly
- **ADR-015** (skill vs workflow invocation syntax) — skills are `@`-invoked + judgment-heavy; workflows are `/`-invoked + deterministic. Pins the "skill or workflow?" decision for `release-manager`.
- **`@propose-extension` SKILL.md** at `~/.codeium/windsurf/skills/propose-extension/SKILL.md` (224 lines) — the dispatch table that every recommendation here will route through
- **`/commit` workflow** at `~/.codeium/windsurf/global_workflows/commit.md` — the precedent forcing-function pattern that `release-manager` mirrors
- **`@verify-l1` skill** — landing pad for post-authoring validation per ADR-017 step 10

Net effect: the original design's *shape* survives; its *paths and routing* need rewriting.

### Why a brainstorm now

Three coupled triggers:

1. **Push-to-`main` incident** during Sprint 1 hardening — the plan's Lesson 1: rules-as-text are insufficient when the unsafe path is the path of least resistance. Same shape as the `no-terminal-oneline-scripts` saga that birthed `/commit`.
2. **Manual gap** — `docs/manual.md:93-95` defers mental-model overview, vertical-spawning conventions, and full release discipline; Cascade B + C handoffs both expose mental-model drift (e.g., "what is a meta-repo?", "where do I open Cascade?").
3. **ADR-017 empirical test** — ADR-017's "Empirical test" clause names this brainstorm as the discoverability test for `@propose-extension`. If a fresh Cascade authoring these items bypasses `@propose-extension`, that's a discoverability bug worth a sprint-review item.

### Known drift (out of scope for this brainstorm; flagged for queue)

`global_rules.md:10` lists workflows at `~/.codeium/windsurf/workflows/<name>.md`, but ADR-016 pinned the canonical path as `~/.codeium/windsurf/global_workflows/<name>.md`. The concise rule contradicts the long-form archive + ADR. Fix path: trim+correct in `global_rules.md` via `@update-horizontal` or fold into `@verify-l1`'s checks. Not addressed here; queued.

---

## 1 — `release-manager` skill

### 1.1 Problem

Cascade pushed Sprint 1 hardening directly to `main` without a branch or PR despite git-flow discipline being "obvious". Same failure mode as the embedded-newline-`-m` violation that ADR-013 fixed via `/commit`. Rules-as-text alone do not restrain the agent when the unsafe path is the path of least resistance.

`main` integrity is currently maintained by:
- vibes ("don't push to main")
- `git status --porcelain` clean-tree gate at sprint close (ADR-008) — *post-hoc*, not preventive
- nothing else

There is no preventive forcing function. This is the gap.

### 1.2 Goals

- Maintain integrity of `main` across every repo Cascade touches
- Branch lifecycle: create → commits → push → PR → CI → merge → cleanup
- Stateful + resumable: re-invoking on the same branch picks up where the previous invocation left off
- Idempotent helper workflows (re-running each is safe)
- Graceful degradation when CI is absent (cascade-system itself has no CI yet)

### 1.3 Non-goals

- Not a CI system itself — defers to `gh pr checks` / `gh run list`
- Not a permanent gate against all `main` writes — cold-start exception exists for `git push -u origin main` immediately after `gh repo create`
- Not a commit-message generator — delegates to `/commit`
- Not branch-protection setup — that's a `/start-project` step at repo creation
- Not auto-merge — merge gate always waits for explicit user approval (per Lesson 1: avoiding the "easy unsafe path" we just fixed)

### 1.4 Constraints (post-Sprint-1 surface)

- **ADR-014**: skill at `~/.codeium/windsurf/skills/release-manager/SKILL.md` (NOT `~/.windsurf/skills/`)
- **ADR-015**: skill (`@release-manager`), not workflow — judgment-heavy + stateful + multi-step
- **ADR-016**: helper workflows at `~/.codeium/windsurf/global_workflows/{branch-start,branch-push-and-pr,ci-watch,branch-merge-and-cleanup}.md`
- **ADR-006**: SKILL.md frontmatter required (`name`, `description`, `activation`, `sources_consulted`, `adapted_for`)
- **ADR-017**: must route through `@propose-extension` (not direct authoring)
- **`no-terminal-oneline-scripts`**: branch-protection setup must use single-line `gh api` invocation, not multi-line config
- **150-line skill length norm** (mattpocock heuristic per `@write-skill`): if procedure exceeds, split helpers into workflows (already the plan)
- **`adapt-from-all`**: consult `refs/superpowers/skills/finishing-a-development-branch/`, `refs/superpowers/skills/using-git-worktrees/`, `refs/superpowers/skills/requesting-code-review/`, `refs/claude-skills/engineering/release-manager/SKILL.md` (490 lines, semver + conventional-commits-heavy — likely browse-only since our scope is branch hygiene not release engineering)

### 1.5 Approaches considered

**(a) Skill-centered orchestrator + 4 helper workflows** *(original plan; recommended)*

Skill at `~/.codeium/windsurf/skills/release-manager/SKILL.md` orchestrates. Helpers (`/branch-start`, `/branch-push-and-pr`, `/ci-watch`, `/branch-merge-and-cleanup`) are independently invocable workflows.

- **Pros**: skill owns invariants + judgment; workflows handle deterministic steps; helpers composable independently; matches ADR-015 discipline (`@` skills stateful; `/` workflows deterministic)
- **Cons**: more files to maintain; routing through `@propose-extension` is multi-step (1 skill + 4 workflows + 1 rule + ADR)

**(b) Workflow-only — separate `/branch-*` commands, no central skill**

All logic in workflows; user composes by invoking each in sequence.

- **Pros**: simpler artifact set; no skill bloat
- **Cons**: no central agent owns the invariants ("`main` is never pushed directly"; "no zombie branches"; "CI must be green or explicit override"). Same rules-as-text failure mode as before. Workflows can't carry stateful judgment ("did we already push? does this PR exist?").

**(c) Rule-only with `/commit`-style forcing function**

`branch-and-pr-required` rule is the sole guardrail; no skill, no workflows beyond the existing `/commit`.

- **Pros**: minimal new surface
- **Cons**: A rule alone is what *failed* in Sprint 1 hardening. The lesson is **need a skill as forcing function**, not a stronger rule. (ADR-013 made the same call for commits.)

**Recommendation: (a)**. The push→PR→CI→merge→cleanup flow has multiple decision points (CI failed? PR description ready? branch deletable?). Only a skill can carry that judgment. Helpers stay as workflows so each is independently invocable (the user can `/branch-start` for a quick branch without invoking the full release flow).

### 1.6 Open design branches

**(i) In-place branch (`git checkout -b`) vs git-worktree default**

- **In-place** (original plan): simpler, one working tree, fewer dirs to track
- **Worktree** (`git worktree add`): allows parallel work without stashing; useful when current tree is dirty

Counter-thought: Sprint 2 has parallel verticals B + C. But verticals run in **separate Cascade sessions** in **separate project directories** (`cascade-system/` for both B and C, but for different concerns) — they are not parallel branches in one repo.

**Recommendation**: in-place default. Worktree is opt-in via `/branch-start --worktree`. Most flows don't need worktrees; the mental model stays simple.

**(ii) CI poll cadence**

Original plan: 10 s, back off to 30 s after 5 unchanged polls.

**Recommendation**: keep. No competing alternative surfaces; the cadence is a non-decision unless empirical friction shows up.

**(iii) Branch-protection setup in `/start-project`**

Per `no-terminal-oneline-scripts`, must use single-line `gh api` (no multi-line JSON body via `bash -c`).

```sh
# safe single-line shape (one PUT, fields as -f flags)
gh api -X PUT repos/:owner/:repo/branches/main/protection \
  -f required_pull_request_reviews.required_approving_review_count=1 \
  -f enforce_admins=false
```

**Caveat**: GitHub branch protection on private repos requires GitHub Pro / Team / Enterprise. Solo dev on free tier with private repos cannot enforce. Behavior: skill checks plan via `gh api /user`, skip-with-warning when unenforceable.

**Recommendation**: include in `/start-project` step 9; gracefully skip on free-tier private. Document the caveat in the skill body + manual.

**(iv) Cold-start exception**

After `gh repo create` + initial commit, `git push -u origin main` is the canonical first push (no upstream yet, no PR can exist).

**Recommendation**: encode the exception in `branch-and-pr-required` rule (§2 below). Skill detects "branch has no upstream + working tree has 1 commit + repo is < 5 minutes old" as cold-start state. If detected → allow direct push; else → block.

**(v) Merge style**

- **Squash** (original plan): linear `main` history; multi-commit PR description preserved in squash body
- **Merge commit**: preserves full history; clutters `main`
- **Rebase**: linear, preserves individual commits; risky on shared branches

**Recommendation**: **squash** as default. User can override per-PR. Linear `main` history matches solo-dev workflow.

**(vi) PR description auto-fill**

Original plan: auto-fills from commit messages.

**Recommendation**: auto-draft + hard-gate user review before opening. Skill drafts; user approves. Skill never opens a PR with an auto-generated description without approval (mirrors `@to-prd`'s approval gate).

**(vii) Stale-branch threshold**

Original plan: 7 days no commits / 14 days no merge → flag.

**Recommendation**: keep, but use event-based not time-based language per `no-time-estimates`. Reframe: "no commits since last sprint close" / "no merge across 1 milestone window". Skill flags; doesn't auto-delete.

### 1.7 Recommendation summary

- **Build** the skill at `~/.codeium/windsurf/skills/release-manager/SKILL.md` (target ≤150 lines; helpers carry the verbose procedures)
- **Build** 4 helper workflows under `~/.codeium/windsurf/global_workflows/` (§4 below)
- **ADR required** per `grill-with-docs` 3-test:
  - *Hard to reverse*: YES — changes the canonical commit-to-`main` flow across every repo
  - *Surprising without context*: YES — "why a skill instead of just rules?" (Lesson 1 from the original plan; same shape as ADR-013's `/commit`)
  - *Real trade-off*: YES — (a) vs (b) vs (c) above; skill-centered chosen for state ownership
- **Reserve next ADR number in INDEX before authoring** (per ADR-009; likely ADR-018)
- **Self-test plan**: this batch's own commit goes through `@release-manager` (the new skill). First real exercise.

---

## 2 — `branch-and-pr-required` rule

### 2.1 Problem

Backstop for §1's skill. If the agent forgets to invoke `@release-manager`, the rule is the always-on reminder. Pairing pattern: rule notices, skill resolves (mirrors `plan-drift-watcher` ↔ `/recalibrate` and `sprint-review-prompt` ↔ `@sprint-review`).

### 2.2 Activation

**(a) `always_on`** — loads in every conversation, hard backstop  
**(b) `model_decision`** — fires only when git-push intent is detected

**Recommendation: `always_on`**. Same shape as `no-terminal-oneline-scripts` (also `always_on`). The cost of missing a fire is high (push to `main` already happened once in Sprint 1 hardening; recovery via PR-after-the-fact is ugly). `model_decision` risks the model deciding "this isn't a git-push context" when it actually is — observed failure mode for at least one `model_decision` rule per Sprint 1 retro.

### 2.3 6000-char budget for `global_rules.md` *(critical constraint)*

> **[CORRECTED — see ADR-018 §"Brainstorm drift correction"]** This section's premise is partially wrong: the trim need was overstated. Per Windsurf docs (`https://docs.windsurf.com/llms-full.txt` position 340), `global_rules.md` is always-on with no per-section activation modes; only workspace rules support `model_decision` activation. Architectural fix shipped in PR #13: `branch-and-pr-required` fits without trim (5822 + ~165 = ~5987 / 6000); `know-your-hardware` deploys workspace-only via `/start-project`, not as a `global_rules.md` entry. No existing rule was trimmed.

**Current state**: 5822 / 6000 chars (178 chars headroom — measured during execution prep)

**Adding `branch-and-pr-required` always_on**: minimum useful concise body ~250–400 chars. **Busts the cap.**

**Mitigation options**:

- **(i) Trim existing rules to free room** — `no-terminal-oneline-scripts` is the longest at ~700 chars; `make-sure-it-works` at ~350 chars; `bidirectional-learning-pipe` at ~360 chars. Could free ~200–300 chars by tightening 2–3 rules
- **(ii) Promote one always_on to model_decision** — risky; rules became always_on for reasons
- **(iii) Make `branch-and-pr-required` itself model_decision** — undercuts §2.2's reasoning
- **(iv) Drop a rule entirely** — defeats purpose

**Recommendation: (i) trim**. Tighten `no-terminal-oneline-scripts` (the long one) since the long-form archive carries the full rationale. Target: free 300+ chars; end state ≤5950 chars (50-char safety margin under cap). Proves out the dual-storage pattern (concise = LAW; long-form = COMMENTARY).

### 2.4 Body shape

Concise (in `global_rules.md`):

```
## branch-and-pr-required

Never `git push` while on `main` in any repo. All changes flow through `@release-manager` (branch → commits → push → PR → CI → merge → cleanup).

Pre-flight before every `git push`: am I on `main`? If yes → STOP, invoke `@release-manager`.

Exception: `git push -u origin main` immediately after `gh repo create` (cold-start, no upstream yet, no PR can exist).
```

Long-form (in `~/Projects/cascade-system/docs/rules/branch-and-pr-required.md`):
- Full frontmatter (`trigger: always_on`, `description`, `sources_consulted`, `adapted_for`)
- Pre-flight checklist (parallels `no-terminal-oneline-scripts` shape)
- Cold-start exception detection logic
- Why-this-exists section (Lesson 1 + Sprint 1 push-to-main incident)
- Reference to `@release-manager` skill
- Interaction with other rules (`make-sure-it-works`, `clean-project-structure`)

### 2.5 Cold-start exception wording

Detection (state-based, not time-based per `no-time-estimates`):
- Branch has no upstream (`git rev-parse --abbrev-ref --symbolic-full-name @{u}` fails)
- Working tree contains exactly 1 commit ahead of empty
- Remote has no `main` branch yet (`gh api repos/:owner/:repo/branches/main` returns 404)

If all three hold → cold-start. Direct `git push -u origin main` allowed.

### 2.6 Consequences

- `global_rules.md` ends at ≤ 5950 chars (verified via `wc -m` per ADR-014 routine)
- `~/Projects/cascade-system/docs/rules/INDEX.md` gains a row
- `@verify-l1` already checks the cap; no new validator needed
- Long-form archive doc is referenced from `@release-manager` skill body

---

## 3 — `know-your-hardware` rule

### 3.1 Problem

User runs M1 Pro 16 GB Metal 4 (no CUDA). Tool / library / model choices must respect ARM-native availability, RAM ceiling, GPU constraints. When the task exceeds local capacity, the agent must escalate to AWS *with budget estimate* before incurring cost. Currently:
- No agent-side awareness of hardware constraints
- AWS escalation is ad-hoc (could spin up a `g5.2xlarge` without surfacing cost)
- ML projects (thesis, research) are imminent (Cascade D)

### 3.2 Layer

**(a) L1** — applies to every project (cost-blast-radius is global)  
**(b) L2** — per-project, only ML-flavored projects  
**(c) L3** — only on `python-ml-uv` template

**Recommendation: (a) L1**. Cost-blast-radius (AWS bills) isn't tied to a project's stack; AWS escalation is a posture, not a stack. A web project asking for "process 80 GB of CSV" is just as relevant as a thesis project asking for "fine-tune 7B". L2 / L3 confine to one stack and lose the cross-project posture.

### 3.3 Activation

> **[CORRECTED — see ADR-018 §"Brainstorm drift correction"]** This section's premise is wrong: `global_rules.md` does not support per-section activation modes. The `(model_decision)` annotations on existing rules in that file are decorative — the file is always-on as a whole per Windsurf docs. Real `model_decision` activation is only available for workspace rules (`<project>/.windsurf/rules/<name>.md`) via `trigger:` frontmatter. Architectural fix shipped in PR #13: `know-your-hardware` lives as long-form archive only, deployed to per-project `.windsurf/rules/` by `/start-project` step 6a — that's where it gets real `model_decision` activation. No `global_rules.md` entry.

**(a) `always_on`** — loads in every conversation  
**(b) `model_decision`** — fires only when resource-heavy intent is detected (training, large datasets, GPU, AWS, > N parallel jobs)

**Recommendation: `model_decision`**. Most conversations don't involve heavy compute — building a Next.js page doesn't need to load the M1 spec block into context. `model_decision` is the right granularity:
- **Saves `global_rules.md` budget**: ~150 chars for a 1-line description vs ~600+ chars for full hardware spec block
- **Triggers correctly**: keywords like "fine-tune", "train", "GPU", "CUDA", "Metal", "AWS", "S3", "EC2", "dataset > N GB" are unambiguous
- **Pairs with the `context7-and-docs-first` precedent** — that rule is also `model_decision`, fires on external-library work

This is the divergence from the original plan, which proposed `always_on`. The divergence is justified by:
1. The 6000-char budget pressure (§2.3 already requires trimming for `branch-and-pr-required`)
2. The trigger keywords are unambiguous → low false-negative risk
3. The cost of a missed fire is "ask user before spending money" vs (in `branch-and-pr-required`'s case) "mutilate `main`" — softer downside

### 3.4 Body shape

Concise (in `global_rules.md`):

```
## know-your-hardware (model_decision)

Local hardware: M1 Pro / 16 GB / Metal 4 / 14 GPU cores / no CUDA. Tool choices respect ARM availability + RAM ceiling + Metal-vs-CUDA. When task exceeds local capacity (training >3B model, processing >50 GB data, requiring CUDA-only library), STOP and surface: what / why / AWS recommendation / budget estimate / ask approval. Never spin up AWS resources without explicit budget-acknowledged approval. Full spec + escalation procedure: `docs/rules/know-your-hardware.md`.
```

Long-form (in `~/Projects/cascade-system/docs/rules/know-your-hardware.md`):
- Full frontmatter (`trigger: model_decision`, full `description`)
- Hardware spec block (verbatim from original plan §2.1)
- Implications for tool selection (ARM, GPU, RAM, CPU, storage)
- AWS escalation policy (5-step procedure: what / why / recommendation / budget / decision)
- Examples (4 from original plan: fine-tune 7B, process 80 GB, inference quantized 7B, build Next.js app)
- Cost guardrails (spot instances, S3 lifecycle, immediate teardown)

### 3.5 Budget-approval pattern

Echoes the human-in-loop pattern from `@sprint-review` (drain queue → approve promotions) and `@docs-refresh` (per-file approval). Specifically:

```
[know-your-hardware fires]

⚠ This task plausibly exceeds local capacity:
  Need:      Fine-tune 7B model on 50k examples
  Why local fails:  16 GB RAM insufficient; no CUDA; MLX fine-tune support partial
  AWS suggestion: g5.2xlarge spot (~$0.36/hr); estimated 6 h → ~$2.20 + $0.50 S3 = ~$3
  Approve / defer / alternative?
```

### 3.6 Open: should escalation include OpenAI / Anthropic API as an alternative?

For tasks that fit "fine-tune for one inference" (e.g., classification, extraction), API call is often cheaper than AWS GPU rental. Original plan mentions Bedrock; doesn't explicitly include OpenAI / Anthropic.

**Recommendation**: include in §3.5's body. Frame as "alternative paths" — local / AWS GPU / managed API / Bedrock. Skill suggests; user picks.

---

## 4 — Helper workflows (`branch-start`, `branch-push-and-pr`, `ci-watch`, `branch-merge-and-cleanup`)

### 4.1 Are 4 distinct workflows the right shape?

**(a) 4 separate workflows** *(original plan; recommended)*  
**(b) 1 unified `/release` with sub-commands**  
**(c) Skill body inlines all procedures, no separate workflows**

**Recommendation: (a)**. Per ADR-015 + ADR-006:

- **Independent invocation**: `/branch-start` standalone (quick branch without full release flow); `/ci-watch <pr>` standalone (check on a PR opened earlier)
- **Composability**: skill calls helpers; user can call helpers directly; the same procedure serves both paths
- **Length budget**: inlining 4 procedures into one SKILL.md busts the 150-line norm (per ADR-006 + mattpocock)
- **Slash-name flatness**: Windsurf workflows are flat names; sub-commands not native (would require parsing args in body — fragile)

### 4.2 Paths (per ADR-016)

```
~/.codeium/windsurf/global_workflows/branch-start.md
~/.codeium/windsurf/global_workflows/branch-push-and-pr.md
~/.codeium/windsurf/global_workflows/ci-watch.md
~/.codeium/windsurf/global_workflows/branch-merge-and-cleanup.md
```

NOT `~/.codeium/windsurf/workflows/` (deprecated per ADR-016) and NOT `~/.windsurf/workflows/` (dead per ADR-014).

### 4.3 Frontmatter

Each workflow per ADR-015:

```yaml
---
description: <one-line; triggers slash-command discovery>
---
```

`description:` is mandatory (no description = no slash-command surfacing per Windsurf scanner).

### 4.4 Inter-workflow contracts

| Workflow | Mutates | Idempotent? | Skill calls when |
|---|---|---|---|
| `/branch-start` | local working tree (creates branch / worktree) | YES — re-run on already-branched tree no-ops with confirmation | Step 1 of release flow |
| `/branch-push-and-pr` | remote branch, opens PR | YES — existing PR returns same URL; doesn't duplicate | Step 3 (after commits land) |
| `/ci-watch` | nothing (poll-only) | YES — pure read | Step 4 (after PR opens) |
| `/branch-merge-and-cleanup` | remote PR (merges + deletes), local branch (deletes), worktree (removes) | NOT idempotent — destructive; skill calls only after explicit user merge approval | Step 6 (after CI green + user approval) |

### 4.5 Shape per workflow (sketch only — full shape during authoring)

- **`/branch-start <topic-slug> [--worktree]`** — `git checkout -b <type>/<topic-slug>` (default) or `git worktree add` (flag); type from conventional-commits (feat/fix/chore/docs/refactor/test). Reports new branch + cwd.

- **`/branch-push-and-pr`** — `git push -u origin HEAD`; `gh pr create --fill` (drafts from commits); skill hard-gates user review of the description before invoking; returns PR URL + number.

- **`/ci-watch <pr-number>`** — `gh pr checks <pr>` poll loop; reports state changes; backs off 10s → 30s after 5 unchanged. Returns control after each cycle (non-blocking). If no CI configured → "no CI; merge gate is manual review only"; exit clean.

- **`/branch-merge-and-cleanup <pr-number>`** — `gh pr merge --squash --delete-branch <pr>` (deletes remote); `git checkout main && git pull && git branch -D <local>`; `git worktree remove <path>` if applicable. Verifies all 4 deletions.

### 4.6 Recommendation summary

Build all 4. Author alongside the skill body (skill references each by slash-name). Routes via `@propose-extension` → "Workflow (L1)" route → file write under `global_workflows/`. No ADR per workflow — they implement §1's ADR; mentioning them in §1's ADR is sufficient.

---

## 5 — Manual deferred sections

### 5.1 Three deferrals per `docs/manual.md:93-95`

| Section | Independent of §1–§4? | Order |
|---|---|---|
| Mental model overview (L0/L1/L2/L3, horizontal vs vertical Cascade, meta-repo vs project repo) | YES | Ship anytime |
| Vertical-spawning conventions (when/why/how to spawn fresh Cascade, handoff prompt structure) | YES | Ship anytime |
| Full release discipline | NO — references `@release-manager` + helper workflows | After §1, §2, §4 land |

### 5.2 Build order

1. Mental model + vertical-spawning ship together as one manual edit (independent; tightly related; user-facing terms cluster)
2. Full release discipline ships after `@release-manager` skill exists (the section *describes* the skill; can't precede it)

### 5.3 Worked-example pattern

Original plan §3.4 proposed an "AI voice coach end-to-end" worked example. Critique:

- **Pro**: concrete; readers can follow the flow
- **Con**: hypothetical project decays — the example is AI voice coach; in 6 months the user's reality has moved on; example becomes stale; nobody updates it
- **Alternative**: use **actual user projects** as worked examples
  - "Starting a project" → use `portfolio-website`'s actual L2 setup (19 rules, 26 skills, 10 workflows per `SETUP_ROADMAP.md`)
  - "Spawning a vertical" → use Cascade B (python-ml-uv L3 template) and Cascade C (Obsidian) handoffs as the canonical examples
  - "Release discipline" → use this brainstorm's own `@propose-extension`-routed shipping as the worked example

**Recommendation**: drop the hypothetical AI-voice-coach example. Use actual project artifacts (portfolio-website, Cascade B/C handoffs, this batch's own dogfooding). Stable; auditable; ages with the system.

### 5.4 Audience tone

Per original plan §3.4: plain English, scannable, runnable examples. Not a reference doc — a *guide*. Length scales to need; no fixed counts (per `no-quantity-over-shape`).

### 5.5 Routing — edge case for `@propose-extension`

Adding sections to an existing manual.md is neither pure "new artifact" nor pure "L1 mutation":

- "New artifact" route → file write at `<path>` — but the file already exists
- "L1 mutation" route → invoke `@update-horizontal` — designed for rule/skill/workflow body edits, not doc additions

**Recommendation**: route via `@update-horizontal` since the artifact (manual.md) exists and we're modifying its body. Even though the *content* is net-new sections. This matches the spirit of `@update-horizontal`'s "modify existing L1 artifact" route.

**Flag**: this is genuinely ambiguous in ADR-017's route table; flagged as Q2 in §8.

---

## 6 — Triage of out-of-scope parked items

| # | Item | Verdict | Reasoning | Re-eval trigger |
|---|---|---|---|---|
| 1 | `system-overview.mmd` (L0/L1/L2/L3 + Cascade A/B/C/D roles, Mermaid) | **defer** | Multi-Cascade flow not yet stabilized; Sprint 2 verticals B + C will exercise it; backfill makes more sense after | After Sprint 2 verticals close |
| 2 | `cross-cascade-flow.mmd` (handoff + sprint-review + queue cycle, Mermaid) | **defer** | Same as #1 — needs vertical-spawn evidence first | After Sprint 2 verticals close |
| 3 | `phase-taxonomy.excalidraw` (hand-drawn) | **drop** | Hand-drawn is opportunistic; no concrete need has surfaced; `docs/architecture/README.md:11` already calls it pending without a forcing trigger | If still pending after Sprint 3, remove from `architecture/README.md` intended-contents per ADR-010 |
| 4 | Meta-repo CI (markdown lint, ADR INDEX consistency, doc placement, link integrity) | **defer** | Blocks on M-2 (L1 storage repo) — CI needs to install L1 surface for `/docs-refresh` validation | After M-2 resolves |
| 5 | M-2: L1 storage as git repo | **defer with explicit owner** | Sprint-1 review queue item; needs focused `@grill-me` of its own (3 candidates: dotwindsurf repo / `cascade-system/l1/` symlink / sync script). Higher priority than #4 since it blocks #4 | Before Sprint 3 OR before next `@update-horizontal` real invocation, whichever is sooner |
| 6 | `skill-by-skill` (deferred portfolio-website rule) | **defer** | Sprint 1 retro: needs vertical evidence | A Sprint 2 vertical reports focus loss (Cascade B or C) |
| 7 | `local-demo-before-push` (deferred) | **likely permanent absorption — hold to confirm** | Already absorbed into `make-sure-it-works`; Sprint 2 will validate | If `make-sure-it-works` proves too abstract in any Sprint 2 retro |
| 8 | `config-yaml-first` (deferred) | **move to L3 (not L1)** | Belongs to `nextjs-app` L3 template (when authored). Not a global rule — stack-specific. | First `nextjs-app` project starts (post-Cascade B / D) |
| 9 | `github-project-management` (deferred) | **likely permanent absorption — hold to confirm** | Absorbed into `@sync-github` skill | If `@sync-github` duplicates per-repo logic across Sprint 2 verticals |
| 10 | SKILL.md length heuristic (mattpocock 100 vs our 150–300) | **defer** | Need vertical evidence; Cascade B and C will produce L3 skills with their own length distributions | After Cascade B and C report length-related friction (or don't) |
| 11 | Multi-Cascade ADR numbering coordination | **defer** | Single collision (ADR-004) so far; post-hoc renumber proved cheap (Sprint 1 retro friction note); pre-existing reservation discipline (ADR-009) covers the new normal case | After 2nd collision OR after a post-hoc renumber proves expensive |
| 12 | Disk-headroom check | **drop** | One-off incident (Sprint 1 disk-full mid-M1.2); not pattern-bearing; would be operational tooling not a rule | Don't re-eval unless recurrence |
| 13 | Queue archive policy (`queue/archive/.gitkeep` lazy-create) | **defer (trivial)** | Create on first dropped item; Sprint 2's first `@sprint-review` should exercise the drop path | Sprint 2 first `@sprint-review` |

**Cross-cluster note**: items #4 and #5 are coupled — #4 blocks on #5. Items #6–#9 are the queue's M1.1 deferrals; their re-eval triggers are already logged in `queue/pending-review.md` so Sprint 2's `@sprint-review` will revisit naturally. No action here for those.

---

## 7 — Build order + routing through `@propose-extension` (ADR-017 empirical test)

Recommended order. Each item is invoked via `@propose-extension`, which dispatches per its route table.

| # | Item | Type | `@propose-extension` route | ADR? | Rationale for ordering |
|---|---|---|---|---|---|
| 1 | `branch-and-pr-required` rule | Rule (L1) | Dual-author archive + `global_rules.md` (with trim of existing rules to fit cap) | NO (per 3-test: hard-to-reverse YES; surprising-without-context NO — direct echo of `no-terminal-oneline-scripts` shape; real-trade-off NO — `always_on` is forced by §2.2's reasoning) | Fastest unlock; small surface; forces the global_rules.md trim discipline upfront |
| 2 | `know-your-hardware` rule | Rule (L1) | Dual-author archive + `global_rules.md` (`model_decision` shape, light) | NO (3-test: hard-to-reverse NO — could move to L2 later; surprising-without-context partial; real-trade-off — yes for layer + activation but small) | Independent of #1; can ship in parallel; light global_rules.md addition |
| 3 | `release-manager` skill | Skill (L1) | `@write-skill` | **YES — ADR-018 (likely)** (3-test: all three hold per §1.7) | Depends on #1 + #2 being context for the skill body; ADR captures the (a) vs (b) vs (c) decision |
| 4 | 4 helper workflows | Workflow (L1) ×4 | File write under `global_workflows/` for each | NO per workflow — they implement #3's ADR | Author alongside #3; skill body references them |
| 5 | Manual: mental model + vertical-spawning sections | L1 doc edit | `@update-horizontal` | NO (mechanical addition of deferred content) | Independent of #1–#4; ships anytime; resolves user-facing mental-model gap |
| 6 | Manual: full release discipline section | L1 doc edit | `@update-horizontal` | NO | Blocks on #3 + #4 landing (the section describes them); naturally follows |

**Empirical test of ADR-017 — checkpoint**:

> "If a fresh Cascade authoring those items bypasses this skill, that's a discoverability bug worth a sprint-review item." — ADR-017:80

Self-test: was `@propose-extension` discoverable from this brainstorm? Cascade A authored this artifact; recommendation routes through `@propose-extension`; the next session that picks up #1 should auto-discover the skill via its `description` matching "I want to add a new rule". If the next Cascade starts authoring `branch-and-pr-required` *without* invoking `@propose-extension`, that's the discoverability bug. Capture to `pending-review.md` as Sprint 2 review item.

**Bootstrap note** (per ADR-017:82): `@propose-extension` itself was authored without routing through itself (chicken-and-egg). The same exemption does NOT apply to items #1–#6 — all must route.

### 7.1 Single ADR or one per item?

`@release-manager` skill (#3) gets its own ADR (ADR-018). Items #1, #2, #4 are scoped within that ADR (rules + workflows are #3's supporting layers per the original plan's §0). Items #5, #6 are mechanical doc edits — no ADR.

**Alternative considered**: one ADR per artifact. Rejected — fragments a coherent design across 4–5 ADRs; reader has to reconstruct the cluster.

### 7.2 Sprint placement

Insert these milestones into Sprint 2 alongside verticals B + C, OR carry to Sprint 3?

- **Argument for Sprint 2**: Vertical B's first `/start-project --dry-run` exercises `/start-project` which (post-#1) sets branch protection. If #1–#3 don't ship before Vertical B's M2B.6, Vertical B can't validate the protection step. Coupling is real.
- **Argument for Sprint 3**: Sprint 2 is already scoped (M2B.1–M2B.7 + M2C.1–M2C.6); adding 6 items mid-flight violates `plan-drift-watcher`'s "scope creep" signal.

**Recommendation**: Sprint 2 mid-sprint amendment via ADR (precedent: ADR-004 was a mid-Sprint-1 amendment that landed cleanly). Items #1, #2, #5 can be parallelized with verticals (different Cascade sessions). Items #3, #4, #6 are coupled and ship as one batch. Total addition: 6 milestones to Sprint 2 (call them M2A.1–M2A.6 — Vertical A, the horizontal Cascade A's own work alongside B and C).

---

## 8 — Open questions (need user input before #1 ships)

**Q1** — Output path for this brainstorm: `docs/architecture/parked-items-brainstorm.md` (your stated path; expands `architecture/`'s scope from diagrams-only) vs `docs/parked-items-brainstorm.md` (root; closer to manual.md).

*Recommendation*: keep `docs/architecture/parked-items-brainstorm.md`. Add a one-line note to `docs/architecture/README.md` clarifying scope ("also hosts cross-cutting design brainstorms when no `docs/prompts/stages/` exists in this meta-repo"). One-line solution; avoids an ADR for a single-file placement decision.

**Q2** — Manual.md section additions (§5): `@propose-extension` route is ambiguous. New content but existing artifact. `@update-horizontal` (recommendation) or "new artifact + path write" (alternative)?

*Recommendation*: `@update-horizontal`. Possibly worth a clarifying amendment to ADR-017's route table ("modify existing L1 doc — even adding new sections — routes via `@update-horizontal`").

**Q3** — `release-manager` worktree default: in-place (recommendation) vs worktree default. Sprint 2 verticals run in separate Cascade sessions in separate dirs, so worktree-as-default isn't motivated by parallel-vertical work. But user may have other parallel-work patterns.

*Recommendation*: in-place default; `--worktree` flag opt-in.

**Q4** — `global_rules.md` 6000-char budget mitigation for §2.3:
- Trim `no-terminal-oneline-scripts` (the ~700-char rule) by ~150–200 chars
- Trim `make-sure-it-works` by ~50–80 chars
- End state: ≤5950 chars
- Long-form archives carry the full rationale

*Recommendation*: yes, trim. Worth its own micro-ADR? No — trimming concise text while preserving the rule's invariant is mechanical (per `document-as-you-go` significance test). Note in commit body.

**Q5** — Branch protection on free-tier private repos: skip-with-warning (recommendation) vs require user upgrade. Free-tier private repos cannot enforce branch protection.

*Recommendation*: skip-with-warning. Document in skill body + manual. User upgrade is out of scope.

**Q6** — Drift in `global_rules.md:10` (lists `workflows/` instead of `global_workflows/` per ADR-016). Out of scope for this brainstorm but real. Fix path?

*Recommendation*: fold into `@verify-l1`'s checks (path-correctness validator) AND ship as a small `@update-horizontal` invocation alongside #1 (since both edit `global_rules.md`).

**Q7** — Sprint placement (§7.2): Sprint 2 mid-sprint amendment (recommendation) vs Sprint 3.

*Recommendation*: Sprint 2 amendment via ADR; precedent is ADR-004. Items parallelize with verticals.

---

## 9 — Next phase

This is a brainstorm artifact, not a PRD. No `@to-prd` or `@to-issues` invocation here.

**On approval of this brainstorm**:

1. Resolve §8 Q1–Q7 (single response message OK; one decision per question).
2. Reserve ADR-018 in `docs/decisions/INDEX.md` (per ADR-009).
3. Invoke `@propose-extension` for item #1 (`branch-and-pr-required` rule). Skill walks Q1–Q5, runs `adapt-from-all` pass, presents route + ADR-018 draft, dispatches.
4. Continue per §7 build order.

**On revision request**:

Edit this file in place; status stays Draft. Re-submit for review.

**Provenance**:

- Inputs: `docs/manual.md`, handoffs B + C, `queue/pending-review.md`, `retros/sprint-1.md`, ADR-014/015/016/017, `~/.windsurf/plans/post-sprint-1-hardening-batch-2-cac5f9.md`
- Mode: synthesized artifact (grill-me discipline batched, per user selection in plan-mode)
- Plan: `~/.windsurf/plans/parked-items-brainstorm-6984b3.md`
- Empirical-test target: ADR-017
