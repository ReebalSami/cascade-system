# Cascade system manual

The day-to-day operating guide for the Cascade lifecycle system. Covers what lives where, how to make changes safely, and how to start new work. Authoritative for "where" questions; refer to ADRs for "why" questions.

> **Looking for a scannable inventory?** See `docs/cheat-sheet.md` for the one-page list of every skill, workflow, rule, contract, and template, plus how to start work (`@begin` / `@kickoff`) and how to extend the system (`@propose-extension`). The cheat-sheet says *what*; this manual says *why*.

## Where things live

The L1 layer (global rules, skills, workflows, contracts, templates) is split across two roots because Windsurf only scans one of them and global rules have a hard size cap. Per ADR-014:

| Component | Canonical path | Notes |
|---|---|---|
| **Skills (global)** | `~/.codeium/windsurf/skills/<name>/SKILL.md` | Multi-file. Discovered automatically by Windsurf. Frontmatter: `name`, `description`, `activation`, `sources_consulted`, `adapted_for`. |
| **Workflows (global)** | `~/.codeium/windsurf/global_workflows/<name>.md` | Single-file each. Slash-command name = filename. Frontmatter: `description`. (Per ADR-016; `~/.codeium/windsurf/workflows/` is deprecated.) |
| **Rules (global, concise/active)** | `~/.codeium/windsurf/memories/global_rules.md` | **Single file, ≤6000 chars.** Always-on, no frontmatter. The LAW that loads in every conversation. |
| **Rules (global, long-form/reference)** | `~/Projects/cascade-system/docs/rules/<name>.md` | Multi-file with full frontmatter, rationale, provenance. The COMMENTARY. Not auto-loaded; available via reading. |
| **Rules (per-project)** | `<project>/.windsurf/rules/<name>.md` | Workspace-scoped. Loaded only when Cascade is opened inside that project repo. Populated by `/start-project` step 6a from the long-form archive. |
| **Skills (per-project)** | `<project>/.windsurf/skills/<name>/SKILL.md` | Workspace-scoped. Populated by `/start-project` step 6b from the L3 template if any. |
| **Workflows (per-project)** | `<project>/.windsurf/workflows/<name>.md` | Workspace-scoped. Populated by `/start-project` step 6b. |
| **Contracts (L1)** | `~/.windsurf/contracts/<name>.md` | Agent-internal — not Windsurf-loaded; consumed by skills/workflows that read them explicitly. |
| **Templates (L3)** | `~/.windsurf/templates/<type>/` and `~/.windsurf/templates/_shared/` | Agent-internal; consumed by `/start-project` and `/add-project-type`. |
| **Plans (current/historical)** | `~/.windsurf/plans/<slug>-<suffix>.md` | Cascade-authored plans before implementation. Not Windsurf-loaded. |
| **ADRs** | `~/Projects/cascade-system/docs/decisions/ADR-NNN-<slug>.md` (meta-repo) or `<project>/docs/decisions/ADR-NNN-<slug>.md` (downstream) | One ADR per significant decision. Reserve NNN in `INDEX.md` first (per ADR-009). |
| **Retros** | `~/Projects/cascade-system/retros/sprint-N.md` (meta-repo) or `<project>/docs/retros/<milestone-slug>.md` (downstream) | Immutable once `Status: closed` (per ADR-011). |
| **Handoffs** | `~/Projects/cascade-system/docs/handoffs/<cascade-id>-<scope>.md` | Status changes via INDEX.md update; file content stays. |

### Paths that are dead

- `~/.windsurf/skills/`, `~/.windsurf/workflows/`, `~/.windsurf/rules/` → Windsurf does not scan them. The migration on 2026-04-30 (ADR-014) emptied these. Anything authored there will be invisible to Cascade.

### Cross-agent fallback paths (informational only)

Windsurf also discovers skills under `~/.agents/skills/` and `.agents/skills/` for cross-agent compatibility (and `~/.claude/skills/`, `.claude/skills/` if Claude Code config reading is enabled). We do **not** use these as primary — `~/.codeium/windsurf/` is canonical.

## How to update an L1 component

Always route through `@update-horizontal`. Never edit L1 from outside that skill (exception: initial milestone authoring or `@write-skill` for new skills).

For rule edits specifically, the skill enforces dual-update: long-form archive in `~/Projects/cascade-system/docs/rules/<name>.md` AND the corresponding section in `~/.codeium/windsurf/memories/global_rules.md`. The two are siblings, not source/derived. Run `wc -c ~/.codeium/windsurf/memories/global_rules.md` after every rule edit; must remain ≤6000.

For verification: invoke `@verify-l1` to confirm the layout is consistent before declaring an L1 change complete.

## How to start a new project

```
/start-project [<name> <type>]
```

The workflow walks 15 steps (validate type → scaffold → copy global rules → GitHub repo → initial commit → push → branch protection → Project v2 → milestones → brainstorm phase → handoff). See `~/.codeium/windsurf/global_workflows/start-project.md` for the procedure.

Recommended: hand off to a fresh Cascade after `/start-project` completes — running phases in a context-cleared session prevents state bleed.

## How to run a phase

```
/run-phase <phase-name>
```

Looks up the phase in the project's `.windsurf/phases.yaml`, runs pre-checks, invokes the phase's skill, runs post-checks, hints at the next phase. Contract source: `~/.windsurf/contracts/phase-taxonomy.md`.

## Sprint lifecycle

1. Sprint plan locked into `~/.windsurf/plans/<sprint-slug>.md` (or amended per ADR if mid-sprint changes).
2. Phases execute via `/run-phase` per project `phases.yaml`.
3. Drift detected by `plan-drift-watcher` rule → `/recalibrate` workflow.
4. Milestone closes → `sprint-review-prompt` rule fires → `@sprint-review` skill drains the learning queue.
5. Approved L1 promotions hand off to `@update-horizontal`.
6. Retro closes (with `git status --porcelain` clean — hard gate per ADR-008).

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `@skills:` shows nothing | Skills authored at deprecated `~/.windsurf/skills/` instead of canonical `~/.codeium/windsurf/skills/` | Move them. Run `@verify-l1`. |
| Slash command not in list | Workflow missing `description:` frontmatter, or authored at deprecated path (`~/.codeium/windsurf/workflows/`) instead of `~/.codeium/windsurf/global_workflows/` (per ADR-016) | Add frontmatter or move file to `global_workflows/`. |
| Global rule "not taking effect" | Section missing from `global_rules.md`, OR file exceeds 6000 chars (silent truncation) | `wc -c` the file; ensure rule has a `## <name>` section. |
| Per-project rule out of date | Global rule edited after `/start-project` ran; per-project copy is stale | Manual `cp` from archive, or run `/sync-rules` (planned). |
| Cascade ignored a rule | Rule is `model_decision`-triggered and the description didn't match the conversation context | Either rewrite the description for better activation OR promote to `always_on`. |

## Pointers

- ADR-014 — the migration record for the L1 canonical paths
- ADR-016 — workflow canonical path correction (`global_workflows/`)
- ADR-015 — skill vs workflow invocation syntax (`@` vs `/`)
- ADR-017 — `@propose-extension` as the single intake channel for system extensions
- ADR-006 — skill frontmatter schema
- ADR-003 — strict docs structure
- ADR-008 — clean working tree gate before closing retros
- ADR-011 — record retention (supersession over deletion)
- ADR-013 — `/commit` workflow as forcing function
- `docs/rules/INDEX.md` — the long-form rule archive index
- `docs/decisions/INDEX.md` — all ADRs
- `~/.codeium/windsurf/memories/global_rules.md` — the LAW (concise, always-on)

## Invocation cheat-sheet

| Intent | Syntax | Auto-activates? |
|---|---|---|
| Use a skill | `@<skill-name>` | Yes — description-match on user phrasing |
| Browse skills in a dropdown | `@skills:` then pick | n/a |
| Use a manual rule | `@<rule-name>` | n/a (manual rules are never auto-loaded) |
| Run a workflow | `/<workflow-name>` | Never auto |
| Browse workflows in a dropdown | `/` then pick | n/a |

`@rules:` dropdown shows only `manual`-trigger rules plus any `AGENTS.md` — by Windsurf design (docs [chunk 340](https://docs.windsurf.com/llms-full.txt)). Always-on and `model_decision` rules are silently active in every message; they are **not** listed in any dropdown and do **not** need (or benefit from) `@rules:` invocation. We currently have zero manual-trigger rules.

Workflows are slash-only. The `@` prefix does not resolve workflow names (docs [chunk 362](https://docs.windsurf.com/llms-full.txt): *"Manual only via `/[workflow-name]` slash command"*).

The `docs/rules/` long-form archive is a human/agent reference; Windsurf does not scan it as rules (ADR-014 hybrid model). Active rules live in `~/.codeium/windsurf/memories/global_rules.md`.

Source: `https://docs.windsurf.com/llms-full.txt` chunks 338, 340, 352, 362.

## Mental model

Cascade operates across four layers. Each layer has a distinct authoring discipline; conflating them is the most common source of "where does this belong?" confusion.

| Layer | What lives here | Canonical location | Edit discipline |
|---|---|---|---|
| **L0** | User + Windsurf IDE + system shell | Fixed substrate | n/a — not authored |
| **L1** | Cross-project system: global rules, skills, workflows, contracts, L3 templates, meta-repo ADRs | `~/.codeium/windsurf/` (active surface) + `~/Projects/cascade-system/` (long-form docs) | `@propose-extension` dispatches to `@write-skill` / `@update-horizontal` / direct-write by route |
| **L2** | Per-project: project-scoped rules, skills, workflows, handoffs, retros, ADRs | `<project>/.windsurf/` + `<project>/docs/` | Direct authoring within the project; per-project rules may mirror L1 long-form archive via `/start-project` step 6a |
| **L3** | Project-type templates (Python ML, Next.js, thesis/research, etc.) | `~/.windsurf/templates/<type>/` | `/add-project-type` to bootstrap; `@write-skill` + `@update-horizontal` to modify |

### Meta-repo vs project repo

Two distinct git repos carry different responsibilities:

- **`cascade-system/`** — the L1 meta-repo: long-form documentation (ADRs, rule archive, handoffs, retros), learning queue, vendored reference skills in `refs/`. Immutable history via conventional-commits + PR discipline. Active L1 surface (`~/.codeium/windsurf/{skills,global_workflows,memories}/`) is operationally separate and currently unversioned (M-2 storage decision deferred).
- **`portfolio-website/`, `hafenhut/`, `FH-Wedel-thesis/`, …** — project repos carrying L2 artifacts alongside domain code. Each has its own `.windsurf/` with project-scoped rules/skills/workflows.

The two are coupled through `/start-project` (seeds a new project repo with a snapshot of relevant L1 rules) and `@update-horizontal` (propagates L1 changes to downstream projects when an L1 rule is modified).

### Horizontal vs vertical Cascade

A single Windsurf workspace hosts one Cascade session. In practice we run multiple sessions in parallel, each with a distinct role:

| Role | Where opened | Purpose |
|---|---|---|
| **Horizontal (Cascade A)** | `~/Projects/cascade-system/` | Meta-maintenance: authors/modifies L1, drains the queue via `@sprint-review`, handles cross-project propagation via `@update-horizontal` |
| **Vertical (Cascade B, C, D, …)** | `<project>/` | Project-specific work: implements features, fixes bugs, authors L2 artifacts, opens PRs via `@release-manager` |

Verticals **consume** L1 (read global rules, use global skills, invoke global workflows) but do not **author** L1 directly. Friction, patterns, or missing rules surfaced during vertical work flow back via `bidirectional-learning-pipe` → `cascade-system/queue/pending-review.md` → next `@sprint-review` drain → `@update-horizontal`.

### What `refs/` holds

`cascade-system/refs/` contains vendored reference skill repositories (superpowers, mattpocock-skills, claude-skills, awesome-agent-skills). Read-only per the `adapt-from-all` convention: when authoring a new L1 artifact, consult refs for patterns, cite sources in the artifact's frontmatter, adapt the pattern to our system. Never runtime-import — adapt and vendor.

## Spawning a vertical Cascade

### When to spawn

Signs that work should move to a new vertical Cascade:

- The work is a distinct problem domain (a separate project, a new feature cluster, a one-off experiment)
- Context window pressure in the current session (long histories degrade attention)
- Two agents need to work in parallel on different concerns
- Clean slate desired for a focused sprint (no bleed from prior conversation)

### How to spawn

1. Open Windsurf with the target project directory as workspace (new window or new workspace)
2. Paste the handoff prompt for that vertical (authored in the horizontal Cascade; see examples below)
3. Let the vertical Cascade read its prompt + ambient state, then draft its first plan; hard-gate approval before executing

### Handoff prompt structure

Canonical examples live at `docs/handoffs/`:

- `cascade-b-template-python-ml-uv.md` — bootstrapping an L3 template (Python/ML/uv stack)
- `cascade-c-obsidian.md` — integrating an external tool (Obsidian MCP)

Structure (from those examples):

- **Scope** — 1-paragraph statement of what the vertical owns
- **Context pointers** — the handful of files that define the working state (PRD, relevant ADRs, `phases.yaml`)
- **Non-goals** — explicit exclusions to prevent scope creep
- **First action** — the concrete first step (often `/run-phase <name>`)
- **Escalation protocol** — when to stop and ping the horizontal Cascade (L1 change surfaced, scope expansion needed, blocker)

### Vertical ↔ horizontal coupling

Verticals commit to their project repo via `@release-manager` (same release discipline as horizontal). L1 mutations require escalating to horizontal — the vertical captures to `cascade-system/queue/pending-review.md` with a `#capture` tag, and the next `@sprint-review` drain (run by horizontal) decides promotion.

### Milestone ownership

Each vertical's milestones (e.g., M2B.x for Cascade B, M2C.x for Cascade C) live on the `cascade-system` GitHub Project for horizontal visibility. Per-project issue boards are optional; the meta-repo board is the canonical tracker for sprint rhythm across all verticals.

## Release discipline

The invariant: **`main` in any repo is never pushed to directly outside cold-start.** All changes land via squash-merged PRs. Enforcement is a cluster of four artifact kinds working in concert (per ADR-018):

| Artifact | Type | Role |
|---|---|---|
| `branch-and-pr-required` | Rule (always-on) | Forces pre-flight self-check before any `git push`; the load-bearing backstop |
| `@release-manager` | Skill (auto-activate) | Orchestrates the full lifecycle; owns the invariants, delegates deterministic steps |
| `/branch-start`, `/branch-push-and-pr`, `/ci-watch`, `/branch-merge-and-cleanup` | Workflows | Independently invocable helpers for each deterministic procedure |

### The flow

1. User or Cascade intends to land a change on `main`.
2. `@release-manager` auto-fires on trigger phrases (ship / merge / land / PR / release / push to main).
3. `/branch-start <type>/<topic-slug>` — creates feature branch from `main` (conventional-commits prefix required).
4. Edits happen on branch; commits via `/commit` (multi-paragraph bodies go through tempfile per ADR-013).
5. `/branch-push-and-pr` — pushes branch, drafts PR description from commits, hard-gates user review before opening via `gh pr create --fill`.
6. `/ci-watch <pr>` — polls `gh pr checks` with adaptive cadence (10 s → 30 s back-off). On no-CI, exits clean with "merge gate is manual review only".
7. `/branch-merge-and-cleanup <pr>` — presents 4-option model (merge / keep / close-no-merge / discard); on "merge" runs `gh pr merge --squash --delete-branch`, cleans local branch + worktree if applicable.

### Cold-start exception

`branch-and-pr-required` permits `git push -u origin main` when all three hold (state-based, not time-based per `no-time-estimates`):

1. Current branch has no upstream tracking branch
2. Working tree has exactly 1 commit (no more)
3. Remote has no `main` branch yet (`gh api repos/:owner/:repo/branches/main` returns 404)

Typical trigger: the very first commit of a repo just created via `gh repo create`.

### Branch protection

`/start-project` step 11 calls `gh api -X PUT .../branches/main/protection` to require 1 approving review on `main`. On free-tier private repos, GitHub returns 403 (enforcement requires Pro/Team/Enterprise); the step skips with a warning and the agent-side `branch-and-pr-required` rule carries the load exclusively.

### Worked example: this manual section's own authoring

The three sections above (Mental model, Spawning a vertical Cascade, Release discipline) were authored during the post-Sprint-1 punch-list closure (plan: `~/.windsurf/plans/post-sprint-1-punch-list-4fa159.md`). Execution:

1. `/branch-start chore/punch-list-pre-sprint-2` (in-place, no `--worktree`)
2. Five commits on the branch, one per gap (G6 → G3 → G1 → G8 → G2), each via `/commit`
3. `/branch-push-and-pr` → PR opened on `ReebalSami/cascade-system`
4. `/ci-watch` → cascade-system has no CI configured → exits clean per ADR-018 empirical-test stanza
5. `/branch-merge-and-cleanup` → squash-merge → branch deleted, local + worktree cleaned

This IS the Phase B dogfood test specified by ADR-018 §"Self-test plan" item #10. The PR's diff + history is the live-fire evidence that the cluster works end-to-end.
