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

## 3 — Rules (17)

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

**1 workspace-only** at `<project>/.windsurf/rules/know-your-hardware.md` (deployed by `/start-project` step 6a; trigger: `model_decision`):

- `know-your-hardware` — alerts on resource-heavy intent; AWS escalation requires explicit user approval with budget surfaced

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
| `python-ml-uv` | in progress (Vertical B; M2B.1–M2B.4 shipped — awaits M2B.5–M2B.6) | Pattern A — Python ML / research (uv + jupytext + papermill + mypy + ruff + pytest; tracker-agnostic; stdlib seeding); will be consumed by Master-Thesis vertical (Cascade D) once M2B.6 validates |
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

## Cross-references

- `docs/manual.md` — narrative manual (mental model, "where things live", lifecycle, release discipline). When the cheat-sheet says *what*, the manual says *why*.
- `docs/decisions/INDEX.md` — all ADRs (ADR-014/015/016/017/018 are the highest-leverage reads for this cheat-sheet)
- `docs/architecture/system-overview.mmd` — diagram view
- `cascade-system/AGENTS.md` — the always-on first-touch pointer for fresh Cascades reading this repo
- `~/.windsurf/plans/<slug>-<suffix>.md` — sprint plans (live outside this repo)
