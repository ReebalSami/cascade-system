# Handoff: Cascade D — Master-Thesis project (Sprint TBD)

**From**: Cascade B (vertical, `python-ml-uv` L3 template author — closed at Sprint 2 M2B.8)
**To**: Cascade D (vertical, owns the thesis project's full lifecycle)
**Sprint**: TBD (assigned by Cascade A's next horizontal sprint planning, after the user names the thesis)
**Plan ref**: TBD (Cascade D authors its own design-locked plan during M2D.0 / M2D.1)
**Issuing milestone**: M2B.8 (`ReebalSami/cascade-system#83`)

You are a fresh Cascade picking up the Master-Thesis project. Vertical B (the `python-ml-uv` L3 template) is complete and consumer-ready; this handoff orients you to bootstrap from it. **The thesis name and slug are decided by the user at your first interaction**, not pre-loaded here.

> **Scope discipline (Vertical B handoff §9 inheritance)**: This handoff is **deliberately empty of thesis-domain content**. Cascade D's first `@grill-me` brainstorm defines what the thesis is about. The python-ml-uv template is generic across thesis-style research / paper repro / Kaggle / RL / vision / NLP / LLM-eval. Thesis-specific framing belongs to D's brainstorm artifact, NOT this handoff.

## 1 — Your role

You author the thesis project at `~/Projects/<thesis-slug>/` (path determined by the user at kickoff). The project is bootstrapped from the `python-ml-uv` L3 template via `/start-project <thesis-slug> python-ml-uv`. After bootstrap you run `/run-phase brainstorm` to enter the project's `phases.yaml` chain (per the M2B.2 deliverable).

You do **not** modify L1 (`~/.codeium/windsurf/...`, `~/.windsurf/contracts/`, `~/.windsurf/templates/python-ml-uv/`). If a gap is found in L1 during your work, log it via `bidirectional-learning-pipe` to `cascade-system/queue/pending-review.md` for Cascade A's next horizontal `@sprint-review` to triage.

You are vertical, not horizontal. Stay in scope. The python-ml-uv template's generic acceptance test (handoff §9 of cascade-b-template-python-ml-uv.md) means the template is **not** thesis-specific; your project is.

## 2 — Read these in order before doing anything

| # | Path | Why |
|---|---|---|
| 1 | `~/Projects/cascade-system/README.md` | Top-level orientation |
| 2 | `~/Projects/cascade-system/AGENTS.md` | Always-on cheat-sheet pointer for this meta-repo |
| 3 | `~/Projects/cascade-system/docs/cheat-sheet.md` | Single-page inventory of every L1 skill / workflow / rule / contract / template |
| 4 | `~/Projects/cascade-system/docs/manual.md` | Narrative system manual (mental model, lifecycle, release discipline) |
| 5 | `~/Projects/cascade-system/docs/handoffs/cascade-b-template-python-ml-uv.md` | **The precedent handoff** — read fully for shape; especially §9 scope guardrails (the generic-ML acceptance test the python-ml-uv template was authored against) |
| 6 | `~/Projects/cascade-system/docs/prompts/stages/02-brainstorm-python-ml-uv.md` | Brainstorm artifact for python-ml-uv; explains every B# decision encoded in your project's bootstrap (B1=mypy, B2=jupytext+papermill, B3=`UV_TORCH_BACKEND=auto`, B4=tracker-agnostic, B5=7-phase chain, B6=2 L3 skills, B7=2 L3 rules, B8=stdlib seeding, B9=uv_build, B10=no CI shipped, B11=Python ≥3.14) |
| 7 | `~/Projects/cascade-system/retros/sprint-2-vertical-b.md` | Vertical B retro — covers what shipped + L1 friction queued + L1 proposals; you inherit the friction list |
| 8 | `~/.windsurf/templates/python-ml-uv/scaffold/README.md` | Scaffolded README that ships in your bootstrapped project — read to understand the post-bootstrap rename + deferred decisions (PyTorch installation, experiment tracker, config layer, CI) |
| 9 | `~/.windsurf/templates/python-ml-uv/phases.yaml` | The phase chain you'll execute via `/run-phase`: `literature → brainstorm → spec → issues → experiment → implement → writeup`. `implement` and `writeup` skills (`@tdd`, `@writeup`) are forward-references — they'll be authored at L1 when first invoked |
| 10 | `~/.codeium/windsurf/global_workflows/start-project.md` | The workflow you'll run for bootstrap (read §"Dry-run mode" if you want to preview before live bootstrap) |
| 11 | `~/.codeium/windsurf/skills/{kickoff,grill-me,to-prd,to-issues,sprint-review}/SKILL.md` | L1 skills you'll use in `phases.yaml`'s phase chain |
| 12 | `~/.codeium/windsurf/skills/release-manager/SKILL.md` + helper workflows | For all `main`-bound changes per ADR-018 |
| 13 | `~/Projects/cascade-system/docs/decisions/ADR-014-l1-canonical-storage-paths.md` | L1 paths you'll respect when L1 friction tempts you to author directly (don't — log to queue instead) |
| 14 | `~/Projects/cascade-system/queue/pending-review.md` | Pre-existing queue entries — your work may surface or resolve some; check the M2B.x entries especially (`/start-project` token substitution; mypy override pattern) |

## 3 — Work-breakdown placeholder

**This handoff does NOT pre-decompose your work.** Cascade D's first `@grill-me` brainstorm defines the M2D.x (or M3D.x — sprint number assigned by Cascade A at planning) milestones based on what the thesis actually requires.

What's *certain*:

- **M2D.0 — Project bootstrap** (one milestone): `/start-project <thesis-slug> python-ml-uv`, then post-bootstrap rename of `your-pkg`/`your_pkg` placeholders to your thesis slug, then `make install && make test` to confirm the bootstrap works end-to-end. **L1 enhancement queued**: `/start-project` does NOT yet auto-substitute the placeholders — manual rename per the scaffolded README's "Post-bootstrap rename" section is required at this milestone (see `cascade-system/queue/pending-review.md` M2B.3 entry).

- **M2D.1 — Brainstorm**: `/run-phase brainstorm` runs `@grill-me` against the thesis topic (whatever the user describes at first interaction). Output: `docs/prompts/stages/02-brainstorm.md`. From this artifact + GitHub milestone planning, you decompose the rest of M2D.x.

What's *NOT* certain (deferred to M2D.1 brainstorm):

- The thesis topic, scope, and methodology
- Whether the thesis needs a literature review (most theses do; close the `literature` phase trivially with a 1-paragraph "no relevant prior art" artifact if not)
- Number of experiments, their parameterization, and reproducibility primitives beyond stdlib seeding
- Specific tracker (default `StdoutTracker`; swap to MLflow / W&B / TensorBoard / Aim per your needs at consumption time)
- Specific config layer (default stdlib `@dataclass`; swap to Hydra / pydantic / argparse / typer at consumption time)
- CI scaffold (none shipped; add `.github/workflows/ci.yml` if needed per scaffolded README's copy-paste block)
- Cross-platform PyTorch (default `UV_TORCH_BACKEND=auto`; switch to extras-based pinning per scaffolded README if training moves to Linux GPU clusters)

## 4 — Operating constraints

You operate under all L1 rules in `~/.codeium/windsurf/memories/global_rules.md` (always-on) + workspace rules deployed by `/start-project` step 6a. Highlights:

- **`adapt-from-all`**: every authored skill / rule carries `sources_consulted` + `adapted_for` frontmatter. Consult `cascade-system/refs/{superpowers,mattpocock-skills,claude-skills,awesome-agent-skills}/` first.
- **`document-as-you-go`**: every significant decision becomes an ADR at decision time. ADRs go in `<thesis-project>/docs/decisions/` (project-local, not meta-repo). Number per project's ADR sequence; reserve in `docs/decisions/INDEX.md` first per ADR-009.
- **`no-time-estimates`**: phrase progress in event-based language ("after experiment X validates", not "in two days").
- **`no-quantity-over-shape`**: describe shape, not counts.
- **`no-terminal-oneline-scripts`**: heredocs banned; use `write_to_file` / `edit` / `/commit` for content delivery.
- **`make-sure-it-works`**: every milestone closes only after its DoD is verified. For your work, "verified" = phase artifact exists + relevant `make test` / `make typecheck` passes (where applicable).
- **`bidirectional-learning-pipe`**: capture friction / insights to the meta-repo queue at `cascade-system/queue/pending-review.md` as they appear.
- **`@release-manager` (ADR-018)**: every `main`-bound change in your thesis repo OR cascade-system meta-repo lands via `@release-manager` (branch → commits → push → PR → CI → squash-merge → cleanup). NEVER `git push origin main` directly outside cold-start. Helper workflows: `/branch-start`, `/branch-push-and-pr`, `/ci-watch`, `/branch-merge-and-cleanup`.
- **`/commit` (ADR-013)**: for any commit with multi-paragraph message body. Single-line `git commit -m "subject"` OK for subject-only commits.
- **`@propose-extension` (ADR-017)**: if you observe an L1 gap that warrants a build (rather than just capture), do NOT author L1 directly — route via `@propose-extension`. Capture-only friction goes to `bidirectional-learning-pipe` queue.
- **`uv-discipline` + `notebook-discipline` (M2B.5 L3 rules)**: deployed to your project's `.windsurf/rules/` by `/start-project` step 6b. Use `uv add` (not `pip install`); use `uv run python ...` (not bare `python`); experiments live in `experiments/` as `.py:percent` (NOT `.ipynb`).

ADR routing per ADR-001:
- Issue close + comment → MCP (`mcp3_add_issue_comment` + `mcp3_issue_write`)
- Repo / Project v2 mutations → `gh` CLI with `GITHUB_TOKEN= GH_TOKEN=` token-bypass

## 5 — First action

When invoked via `@kickoff <this-handoff-path>`, your first focused question to the user is:

> **What's the thesis name + slug?** (Free text for the working title; kebab-case for the project slug, e.g., `master-thesis-graph-rl`. The slug becomes both the local directory name `~/Projects/<slug>/` and the GitHub repo name. The thesis name + topic shape your first `@grill-me` brainstorm but are NOT pre-loaded here.)

After the user answers:

1. **`/start-project <slug> python-ml-uv`** — bootstraps the project (creates local dir + repo + Project v2 + per-phase milestones + initial commit + scaffolded brainstorm phase invocation). Workflow handles steps 1–15 per `~/.codeium/windsurf/global_workflows/start-project.md`.
2. **Post-bootstrap rename** — sed-replace `your-pkg` → `<slug>` and `your_pkg` → `<slug-snake>` (where `<slug-snake>` = `<slug>` with hyphens converted to underscores) in scaffold files; rename `src/your_pkg/` → `src/<slug-snake>/`. Per the scaffolded README's "Post-bootstrap rename" section. Validate via `make install && make test`.
3. **`/run-phase brainstorm`** — invokes `@grill-me` against the thesis topic. Output: `docs/prompts/stages/02-brainstorm.md`. Closes M2D.1.
4. From the brainstorm artifact, you (Cascade D) decompose the rest of the thesis's M2D.x milestones via `@grill-me` step 9 → `@to-prd` → `@to-issues`.

## 6 — Termination

Vertical D ends when the thesis project ships per its own brainstorm-defined Definition of Done (likely: thesis writeup submitted; all experiment + implement milestones closed; per-vertical retro written).

Per ADR-008 hard gate: write a per-vertical retro at `cascade-system/retros/sprint-N-vertical-d.md` (sprint number assigned at vertical-close). Status = `closed` requires clean working tree at retro-commit time.

Cascade D's last action mirrors Vertical B's:

> Vertical D (Master-Thesis project at `~/Projects/<slug>/`) complete. Retro at `retros/sprint-N-vertical-d.md`. L1 observations queued for Cascade A's next horizontal `@sprint-review`. The python-ml-uv template's first real consumer-bootstrap exercise is now closed; any L1 enhancement evidence (notably `/start-project` token substitution + `@verify-l3-template`) is empirically validated.

Do **not** start any subsequent vertical (Cascade E onward). That's a separate handoff with its own scope.

## 7 — Provenance

- **Precedent handoff**: `cascade-system/docs/handoffs/cascade-b-template-python-ml-uv.md` (Sprint 1 closer → Vertical B). Shape adopted here verbatim. Vertical B's §9 scope guardrails (no thesis-specific content in the python-ml-uv template) inverse to this handoff's discipline (no thesis-specific content here either; D's brainstorm decides).
- **Issuing milestone**: M2B.8 (`ReebalSami/cascade-system#83`). Authored at Vertical B's M2B.8 closure as the explicit Cascade-D bootstrap orientation. M2B.8 also closes Vertical B definitively.
- **L3 template ref**: `~/.windsurf/templates/python-ml-uv/` — phases.yaml + scaffold + 2 L3 skills + 2 L3 rules. M2B.6-validated via `/start-project --dry-run` (`uv sync` + `make test` + `make lint` + `make typecheck` all pass).
- **L1 friction inherited from Vertical B** (queued, may surface during your work):
  - `/start-project` token substitution gap (M2B.3 + M2B.6 update) — manual placeholder rename required at M2D.0
  - mypy + conditional optional-dep imports `[[tool.mypy.overrides]]` pattern (M2B.6) — already fixed in scaffold; pattern documented for future scaffold extensions
  - `@release-manager` separate-PR-vs-bundled discipline clarification (M2B.4)
  - `@write-skill` step 6 line-count heuristic refinement (M2B.4 calibration)
  - `@verify-l3-template` skill proposal (M2B.7 retro)
- **Pattern derived from**: `cascade-system/docs/decisions/ADR-002-handoff-prompts-subdirectory.md` + the Sprint-1-closer precedent (`cascade-a-sprint-1.md`).

---

*This handoff is self-contained for fresh-Cascade pickup. The disk + GitHub state at handoff-author time is the orientation context; no chat history is required to begin.*
