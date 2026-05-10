# Cascade-system cheat-sheet

Single-page scannable inventory. Goal: a fresh reader (or future-you) answers *"what skills/workflows/rules exist and what does each do?"* in under 60 seconds.

**Companion docs**

- `docs/manual.md` — narrative (mental model, lifecycle, release discipline)
- `docs/architecture/system-overview.mmd` — single-diagram view of how the layers + skills + workflows + rules connect
- `docs/skills/INDEX.md` and `docs/workflows/INDEX.md` — pointer indexes (machine-maintained by `@docs-refresh`)
- `docs/rules/INDEX.md` — long-form rule archive

**Authoritative paths** (per ADR-014):

- Active L1 surface: `~/.codeium/windsurf/skills/<name>/SKILL.md`, `~/.codeium/windsurf/global_workflows/<name>.md`, `~/.codeium/windsurf/memories/global_rules.md`
- L1 contracts + templates: `~/.windsurf/contracts/`, `~/.windsurf/templates/`
- L2 workspace overlays: `<project>/.windsurf/{rules,skills,workflows}/`
- Plan files: `~/.windsurf/plans/<slug>-<suffix>.md`

---

## 1 — Skills (13)

`@<skill-name>` — auto-activated by description match. Per ADR-015, skills are stateful, judgment-heavy, multi-step.

| Skill | Purpose | Phase / when |
|---|---|---|
| `@grill-me` | Socratic interview that walks the design tree until shared understanding | brainstorm phase, plan stress-tests, "too many implicit assumptions" |
| `@to-prd` | Convert approved brainstorm artifact → PRD using shared template | spec phase |
| `@to-issues` | Decompose PRD §11 vertical slices → GitHub issues + milestones + Project cards | issues phase |
| `@sync-github` | Reconcile Project board with repo signals; flag naked commits; idempotent | board has drifted; before `@sprint-review` |
| `@sprint-review` | Heartbeat retro: drain queue, write retro, propose L1 promotions, hand off to `@update-horizontal` | milestone close |
| `@update-horizontal` | Apply L1 change (rule/skill/workflow/contract/template); writes change ADR; propagates downstream | invoked by `@sprint-review` after L1 promotion |
| `@verify-l1` | Validate L1 storage layout against ADR-014; check rule cap; flag drift | read-only audit, idempotent |
| `@docs-refresh` | Validate + regenerate `docs/` placement; regenerate INDEX files; audit diagrams | after ADR/handoff/retro changes |
| `@release-manager` | Orchestrate branch lifecycle: branch → commits → push → PR → CI → squash-merge → cleanup | every `main`-bound change |
| `@propose-extension` | **Single intake channel** for any system extension; 5-question interview, route table | "I want to add a new rule/skill/workflow" |
| `@write-skill` | Author/edit a skill body (frontmatter, structure, sources_consulted) | invoked by `@propose-extension` for skill routes |
| `@vault-research` | Surface ranked vault notes for a topic via structural composite ranking. READ bookend of `@vault-distill` | `@grill-me` / `@to-prd` vault-context load, `/recalibrate` vault-drift triage, standalone topic exploration |
| `@vault-distill` | Compile raw vault sources → `wiki/` cards per ADR-023 §8 ingest workflow; dry-run tempdir preview + `--commit` atomic write. WRITE bookend of `@vault-research` | ingest clippings / papers / books / _inbox into vault; dogfood ADR → `wiki/sources/adrs/` per ADR-030 |

Canonical paths: `~/.codeium/windsurf/skills/<name>/SKILL.md`. Maintained index: `docs/skills/INDEX.md`.

---

## 2 — Workflows (9)

`/<workflow-name>` — slash-only, no auto-activation. Per ADR-015, workflows are deterministic procedures.

| Workflow | Purpose |
|---|---|
| `/start-project` | Bootstrap new project from L3 template (15 steps): scaffold + repo + Project + milestones + first-phase handoff |
| `/run-phase <name>` | Dispatcher — reads `<project>/.windsurf/phases.yaml` and invokes the named phase's skill (no arg = list phases with status) |
| `/recalibrate` | Detect/resolve drift between PRD §11, GitHub state, recent commits; per-finding triage |
| `/add-project-type` | Bootstrap new L3 template at `~/.windsurf/templates/<type>/` (12 steps + ADR) |
| `/branch-start` | (helper for `@release-manager`) — start branch or worktree |
| `/branch-push-and-pr` | (helper) — push + `gh pr create --fill` |
| `/ci-watch` | (helper) — watch PR's CI status with adaptive cadence; clean exit when no CI configured |
| `/branch-merge-and-cleanup` | (helper) — squash-merge + delete branch + sync `main` |
| `/commit` | Safe multi-paragraph git commit (tempfile + `git commit -F`); bypasses macOS-Windsurf newline crash (per ADR-013) |

Canonical paths: `~/.codeium/windsurf/global_workflows/<name>.md`. Maintained index: `docs/workflows/INDEX.md`.

---

## 3 — Rules (19)

**16 always-on** in `~/.codeium/windsurf/memories/global_rules.md` (single file, ≤6000 char cap, no per-rule frontmatter).

| Group | Rules |
|---|---|
| **Path / storage discipline** | `l1-canonical-paths`, `clean-project-structure` |
| **Shell safety** | `no-terminal-oneline-scripts` |
| **Git / release discipline** | `branch-and-pr-required` |
| **Tone / honesty** | `be-honest-direct-critical`, `anti-laziness-core-principles` |
| **Reasoning quality** | `no-half-knowledge`, `make-sure-it-works`, `context7-and-docs-first` |
| **Documentation** | `document-as-you-go`, `adapt-from-all` |
| **Plan / lifecycle hygiene** | `no-quantity-over-shape`, `no-time-estimates`, `bidirectional-learning-pipe`, `plan-drift-watcher`, `sprint-review-prompt` |

**3 workspace-only** at `<project>/.windsurf/rules/<name>.md` (deployed by `/start-project` step 6a; trigger: `model_decision`; long-form lives in `docs/rules/`; **no concise entry** in `global_rules.md` per Windsurf-treats-global-as-always-on constraint):

- `know-your-hardware` (ADR-018) — alerts on resource-heavy intent; AWS escalation requires explicit user approval with budget surfaced
- `obsidian-context-priming` (ADR-028) — primes Cascade on the Obsidian vault at session start when the active project has vault co-location; three-tier load with privacy guardrails
- `model-selection-advisor` (ADR-034) — emits one-line advisory when phase-typed signals suggest switching to Sonnet 4.6 1M (code-heavy) or Opus 4.7 (decision-heavy); points to `@handoff-to-coding-session` / `@handoff-to-thinking-session`; never auto-switches

Long-form archive: `docs/rules/<name>.md`. Index: `docs/rules/INDEX.md`.

---

## 4 — Contracts (1)

| Contract | Purpose | Path |
|---|---|---|
| `phase-taxonomy` | Schema for L3 templates' `phases.yaml` files (consumed by `/run-phase`, `/recalibrate`, `@sprint-review`, `plan-drift-watcher`) | `~/.windsurf/contracts/phase-taxonomy.md` |

---

## 5 — L3 templates

| Template | Status | Purpose |
|---|---|---|
| `_shared/` | active | universal `docs/` structure + INDEX files + `strict-docs-placement` rule (applied first by `/start-project` two-pass scaffold per ADR-004) |
| `python-ml-uv` | active (Vertical B closed at M2B.8; M2B.1–M2B.8 shipped + validated) | Pattern A — Python ML / research (uv + jupytext + papermill + mypy + ruff + pytest; tracker-agnostic; stdlib seeding; `notebook-discipline` + `uv-discipline` L3 rules); validated via `/start-project --dry-run`: `uv sync` + `make test` + `make lint` + `make typecheck` all pass; ready for consumer bootstrap (see `docs/handoffs/cascade-d-master-thesis.md` for Master-Thesis vertical entry point) |
| `nextjs-app` | not built yet | Pattern B — TypeScript full-stack; defer until first project demands it |
| `python-pipeline` | not built yet | Pattern D — data pipeline; defer until first project demands it |

Templates live at `~/.windsurf/templates/<type>/` with `phases.yaml` + `scaffold/` + optional `rules/` + `skills/` + `workflows/` overlays.

---

## 6 — How to extend the system

**Single intake channel: `@propose-extension`** (per ADR-017). It interviews you with 5 questions (build vs capture, artifact type, layer, problem, activation/trigger), runs an `adapt-from-all` consultation pass, decides whether an ADR is needed, presents a route + (optional) ADR draft for your approval, then dispatches.

| Artifact you want to add or modify | `@propose-extension` route |
|---|---|
| New skill (any layer) | invokes `@write-skill` |
| New rule (any layer) | direct authoring (L1 dual: long-form archive + concise `global_rules.md`; L2/L3: workspace/template path) |
| New workflow (any layer) | direct authoring at canonical path |
| New contract (L1) | direct authoring at `~/.windsurf/contracts/<name>.md` |
| New L3 project type | invokes `/add-project-type` |
| Modify existing L1 artifact | invokes `@update-horizontal` |
| New shared scaffold asset | direct authoring at `~/.windsurf/templates/_shared/<path>` |
| AGENTS.md (any workspace root) | direct authoring at `<project>/AGENTS.md` |
| Capture-only (defer build) | append to `cascade-system/queue/pending-review.md` |

**Never** author L1 artifacts under `~/.windsurf/skills/`, `~/.windsurf/workflows/`, or `~/.windsurf/rules/` — those paths are dead per ADR-014 and Windsurf does not scan them.

---

## 7 — How to start work

| You want to … | Invoke | What it does |
|---|---|---|
| Start a project from a new idea or brainstorm note | `@begin` | Reads the idea note, runs `@grill-me`, decides L3 stack, dispatches to `/add-project-type` (if needed) → `/start-project` → `/run-phase brainstorm` |
| Pick up a vertical Cascade from a handoff doc | `@kickoff` | Reads handoff + parent plan + cited ADRs + cheat-sheet, determines lifecycle position, files vertical's milestone issues if missing, asks ONE focused starting question |
| Resume work in an existing project | open the project directory | `AGENTS.md` auto-loads + L2 rules auto-load; ask *"what's in flight?"* or invoke `/run-phase` (no arg) to list phases with artifact status |
| Land a change to `main` | `@release-manager` | Orchestrates branch → commits → push → PR → CI → squash-merge → cleanup (delegates to 4 helper workflows) |
| Close a sprint milestone | `@sprint-review` | Drains queue, writes retro, decides L1 promotions, hands off to `@update-horizontal` |

---

## 8 — Session ergonomics

### Token-economy practices (per ADR-033)

Two coupled practices for sessions that span sustained authoring. Heuristics, not contracts. Three deferred audit items remain in `queue/pending-review.md` for second-data-point calibration.

| Practice | Action |
|---|---|
| **Fresh session per milestone** | For long-running verticals, open a new Cascade session per significant milestone rather than continuing past ~50k accumulated tokens. `@release-manager` PR-close is the natural break point. Pickup: `@kickoff <handoff-path>` (vertical) or `/run-phase <name>` (mid-vertical). |
| **Response verbosity scales to stakes** | Tight responses for routine updates (milestone closures, validation summaries, queue captures, PR ledgers). Structured (multi-section, multi-bullet) only for decision-gates, handoffs, retrospectives, and ADR drafts. |

Full driver-breakdown + alternatives in ADR-033 §Context. Manual narrative paragraph at `docs/manual.md` §"Token-economy practices".

### When to use which model (per ADR-034)

Windsurf model selection is per-session (set via UI before the session opens), not per-turn. Phase-typed work has a model-fit; switching mid-session means a new Cascade. The `model-selection-advisor` workspace rule emits a one-line advisory on phase-typed signals; the handoff skill pair writes the context bundle for the new session to pick up.

| Phase type | Preferred model | Pickup pattern |
|---|---|---|
| Decision-heavy (PRD, brainstorm, ADR, retro, architectural choice) | **Opus 4.7** | `@handoff-to-thinking-session` → fresh Cascade in Opus 4.7 → `@kickoff <handoff-file>` |
| Code-heavy (implement, experiment, refactor, scaffold edit, test authoring) | **Sonnet 4.6 1M** | `@handoff-to-coding-session` → fresh Cascade in Sonnet 4.6 1M → `@kickoff <handoff-file>` |
| Mixed / routine (queue capture, docs edit, small PR) | Either; stay in current session if under ~50k token threshold (ADR-033) | n/a |

Composes with the fresh-session-per-milestone practice above: `@release-manager` PR-close + phase-type transition is the canonical session-boundary signal. Handoff files live at `~/.windsurf/handoffs/<vertical-or-project-slug>-<YYYYMMDDHHMM>-<phase-type>.md`. Full rationale + alternatives in ADR-034 §Context.

---

## Cross-references

- `docs/manual.md` — narrative manual (mental model, "where things live", lifecycle, release discipline). When the cheat-sheet says *what*, the manual says *why*.
- `docs/decisions/INDEX.md` — all ADRs (ADR-014/015/016/017/018 are the highest-leverage reads for this cheat-sheet)
- `docs/architecture/system-overview.mmd` — diagram view
- `cascade-system/AGENTS.md` — the always-on first-touch pointer for fresh Cascades reading this repo
- `~/.windsurf/plans/<slug>-<suffix>.md` — sprint plans (live outside this repo)
