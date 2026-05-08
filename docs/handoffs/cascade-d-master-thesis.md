# Handoff: Cascade D — HORUS Master-Thesis project (Sprint 2)

**From**: Cascade B (vertical, `python-ml-uv` L3 template author — closed at Sprint 2 M2B.8)
**To**: Cascade D (vertical, owns the thesis project's full lifecycle)
**Sprint**: Sprint 2 (Cascade D runs concurrent with other Sprint 2 verticals)
**Plan ref**: `~/.windsurf/plans/kickoff-cascade-d-horus-362eef.md` (authored at kickoff, 2026-05-07)
**Thesis slug**: `horus` — Hybrid OCR-free Reading & Understanding System (`~/Projects/horus/`; `https://github.com/ReebalSami/horus`)
**Issuing milestone**: M2B.8 (`ReebalSami/cascade-system#83`)

You are a fresh Cascade picking up the HORUS Master-Thesis project mid-vertical. Vertical B (the `python-ml-uv` L3 template) is complete and consumer-ready; HORUS is its first real consumer. M2D.0 (bootstrap), M2D.1 (post-rename + identity), M2D.2 (decision-discipline + source-archival), M2D.3 (literature review), M2D.4 (brainstorm) are closed. See "Current state" below.

> **Scope discipline**: This handoff orients to **HORUS specifically** as of 2026-05-08 (originally authored 2026-05-07). The thesis-domain content lives in `~/Projects/horus/` (`README.md`, `AGENTS.md`, `docs/decisions/ADR-003-brand-naming-horus.md`, `docs/prompts/stages/{01-literature,02-brainstorm}.md`, 46 source stubs under `docs/sources/`) + the pre-loaded brainstorm v2 at `/Users/reebal/Projects/FH-Wedel/SS26/Master-Thesis/research/THESIS_BRAINSTORM_STATE_v2.md`. The `python-ml-uv` template remains generic; HORUS-specific decisions ratified at M2D.0–M2D.4 are the thesis's project-local layer.

## Current state (2026-05-08)

> **If picking up mid-vertical**: M2D.0 through M2D.4 are complete. Next up is M2D.5 (experiment phase kickoff: repo prep + tooling ADRs + first pilot data loop). Phase chain adaptation: `spec` + `issues` phases are deliberately skipped per `02-brainstorm.md` §8 — see "Work-breakdown" for rationale.

| Milestone | Status | Commit / PR |
|---|---|---|
| M2D.0 — Bootstrap (`/start-project horus python-ml-uv`) | ✅ Closed | `1d55c42` (cold-start); GitHub repo + Project v2 board (#6) + 7 phase milestones + branch protection |
| M2D.1 — Post-rename + HORUS identity | ✅ Closed | PR #1 squash-merged at `1559062`; `src/horus/` + `# HORUS` README with `## Why HORUS?` + `AGENTS.md` |
| M2D.2 — Decision-discipline + source-archival | ✅ Closed | PR #2 squash-merged at `4a25d0d`; 2 L2 rules + 3 ADRs (ADR-001/002/003) + `docs/sources/{papers,tools,datasets,legal}/` tree |
| M2D.3 — `/run-phase literature` | ✅ Closed | PR #3 squash-merged at `5f970ec`; 46 source stubs (10 papers + 19 tools + 7 datasets + 10 legal) + `docs/prompts/stages/01-literature.md` synthesis; GitHub milestone `literature-review-complete` closed |
| M2D.4 — `/run-phase brainstorm` | ✅ Closed | PR #4 squash-merged at `2e66fa1`; `docs/prompts/stages/02-brainstorm.md` (status: approved, 162 lines, 8 sections); GitHub milestone `design-tree-resolved` closed. Methodological reframe during walk: NO implementation-phase decisions locked at brainstorm (per user direction) — artefact captures direction + discipline commitments only |
| M2D.5 — `/run-phase experiment` (kickoff) | 🔲 Next | Scope defined in `docs/prompts/stages/02-brainstorm.md` §8: repo structural prep (`data/`, `eval/`, `notebooks/`, `scripts/`) + first tooling-install ADRs (Mustang + MLX + Docling at minimum) + first pilot data loop (Mustang → ≈10–50 synthetic ZUGFeRD invoices → XML-extraction script + ADR → first local VLM on M1 Pro → initial F1 with full rigor per v2 §5.1 + §5.5, MLflow-tracked, deterministic seed) |

Project state:
- Local: `~/Projects/horus/` (main at `2e66fa1`)
- Remote: `https://github.com/ReebalSami/horus` (private; branch-protected, 1 review required)
- Project board: `https://github.com/users/ReebalSami/projects/6`
- Python: 3.14.3 (`.python-version`); torch 2.11.0 installed; `make install && make test` ✅ (4/4 pass) + `make lint` ✅ + `make typecheck` ✅
- Pre-loaded thesis input: `/Users/reebal/Projects/FH-Wedel/SS26/Master-Thesis/research/THESIS_BRAINSTORM_STATE_v2.md` (locked v2; M2D.3 + M2D.4 input)
- Kickoff plan: `~/.windsurf/plans/kickoff-cascade-d-horus-362eef.md`
- Project AGENTS.md: `~/Projects/horus/AGENTS.md` (auto-loads on Cascade activation)

## 1 — Your role

You author the thesis project at `~/Projects/horus/`. The project is bootstrapped from the `python-ml-uv` L3 template (M2D.0). HORUS identity is captured (M2D.1). Decision-discipline + source-archival rules are live (M2D.2). Literature review imported + synthesised (M2D.3). Brainstorm artefact authored as directional-only, no implementation-phase pre-locks (M2D.4). Next: `experiment` phase (M2D.5) — repo prep + tooling ADRs + first pilot data loop. Phase chain skips `spec` + `issues` per `02-brainstorm.md` §8; see §3 Work-breakdown for rationale.

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

## 3 — Work-breakdown (Cascade D decomposition)

Decomposed at kickoff (2026-05-07) per `~/.windsurf/plans/kickoff-cascade-d-horus-362eef.md` §4. Updated at end of each milestone.

**Completed** (see "Current state" above): M2D.0 bootstrap, M2D.1 identity, M2D.2 discipline, M2D.3 literature review, M2D.4 brainstorm.

**Next** (execute in order):

- **M2D.5 — `/run-phase experiment` (kickoff)**: Scope defined in `~/Projects/horus/docs/prompts/stages/02-brainstorm.md` §8. Steps (each gated on its own ADR per `horus-decision-discipline`):
  1. Repo structural prep: `data/` (gitignored), `eval/`, `notebooks/`, `scripts/`
  2. First tooling-install ADRs (NOT bulk install; each `uv add` triggers its own 5-section ADR per ADR-001): Mustang Project + MLX + Docling at minimum; also likely HuggingFace transformers/datasets + PyTorch MPS + MLflow
  3. Dataset downloads (user-action; see §6.2 of `02-brainstorm.md` for the 8-dataset manual-action checklist with URLs + paths + approx sizes — P0: ZUGFeRD corpus + Mustang Project)
  4. First pilot data loop: Mustang generator install + ADR → ≈10–50 synthetic ZUGFeRD invoices → XML-extraction-from-PDF script + script-architecture ADR → first local VLM on M1 Pro on 10 invoices (Granite-Docling via MLX indicated, cohort-selection ADR locks it) → initial F1 reading with **full rigor** (v2 §5.1 standard token F1 + v2 §5.5 field-level error heatmap, MLflow-tracked, deterministic seed; Säring-blocked metrics §5.2/§5.3/§5.4 deferred pending weights + validator stage). Pilot scope small for tractability, NOT for skipping rigor.
  5. After pilot evidence in hand: schedule first Säring technical-progress meeting + draft Säring agenda materials in parallel (RQ + metric spec + hypothesis set + held-out policy + branching tree + budget asks).

- **M2D.6+**: Defined at M2D.5 exit based on pilot evidence. Not pre-decomposed here. Likely shape: iterate on architecture choices (Layer-1 single-shot vs orchestrated, Layer-2 graph backend, Layer-3 retrieval strategy) each gated on its own ADR + experiment cycle. Eventually → writeup phase via `@writeup` (L1 skill, forward-reference).

**Phase-chain adaptation**: L3 `python-ml-uv` `phases.yaml` specifies `brainstorm` → `spec` → `issues` → `experiment`. HORUS **skips `spec` + `issues`** and proceeds directly to `experiment` (M2D.5). Rationale (full version in `02-brainstorm.md` §8): (a) v2 §0–§14 already substitutes for ~70% of typical PRD content; (b) the §4.1 a-priori locks a PRD typically formalises are Säring-blocked (cannot lock until first technical-progress meeting, which is gated on pilot results); (c) authoring a pre-Säring PRD risks HARKing on predictions that aren't pilot-grounded. Revisit at `@sprint-review` if phase-skip proves friction.

**Locked** (carried from kickoff, ratified at M2D.0–M2D.2):

- Thesis topic + scope + methodology core: HORUS = privacy-first document intelligence for German tax/accounting professionals via local VLMs (no OCR pipeline). Source: `THESIS_BRAINSTORM_STATE_v2.md` §0–§3.
- Project identity: HORUS slug + backronym + Egyptian symbolism. Source: ADR-003.
- Decision discipline: every tool/model/library/dataset choice gets a full ADR. Source: ADR-001 + `horus-decision-discipline` rule.
- Source archival: every cited source archived under `docs/sources/<type>/<slug>.md`. Source: ADR-002 + `horus-source-archival` rule.

**Decided in principle, imported as directional working assumptions** (M2D.4 brainstorm imported v2 §0 markers + v2 §3 D1–D11 as direction, NOT as a-priori locks, per user methodological correction; see `02-brainstorm.md` §3 + §7. Each implementation-phase choice gets its own ADR at decision time):

- Single-shot vs. orchestrated VLM pipeline
- VLM model choice (olmOCR-2 / Granite-Docling / LLaVA-Next / Qwen2-VL / others)
- Quantisation / inference framework (MLX, GGUF, native PyTorch on MPS)
- Knowledge-graph layer presence + storage (Neo4j vs. NetworkX vs. in-memory vs. none)
- Retrieval / RAG layer presence + tooling (LightRAG, llamaindex, custom)
- Evaluation corpus (ZUGFeRD, CORD-v2, FUNSD, OmniDocBench, custom German invoices)
- Demo / API surface (FastAPI vs. CLI-only vs. notebook-only)

**Genuinely deferred (no v2 stance)**:

- Experiment tracker (scaffold default: `StdoutTracker` in `src/horus/tracking.py`; swap to MLflow / W&B / TensorBoard / Aim / DVC if needed)
- Config layer (scaffold default: stdlib `@dataclass`; swap to Hydra / pydantic / argparse / typer)
- CI scaffold (none shipped; add `.github/workflows/ci.yml` per scaffolded README copy-paste block if needed)
- Cross-platform PyTorch (scaffold default: `UV_TORCH_BACKEND=auto`; thesis is likely Mac-only — extras-based pinning for Linux-GPU clusters only relevant if cluster training enters scope)

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

## 5 — First action (for a fresh Cascade picking up mid-vertical)

M2D.0–M2D.4 are **already complete**. See "Current state" for exact commits + state.

Your first action is:

1. **M2D.5 — `/run-phase experiment` (kickoff)** — scope defined in `~/Projects/horus/docs/prompts/stages/02-brainstorm.md` §8. Execute in order:
   a. Repo structural prep: add `data/` (gitignored), `eval/`, `notebooks/`, `scripts/` directories + `.gitignore` entries. One PR via `@release-manager`.
   b. First tooling-install ADR: start with Mustang Project (ZUGFeRD generator). Write 5-section ADR per ADR-001 + `horus-decision-discipline` rule. Include Mustang source stub at `docs/sources/tools/mustang-project.md` (likely already exists from M2D.3). PR via `@release-manager`.
   c. Next tooling ADR: MLX + mlx-lm (`uv add`). Same pattern. PR via `@release-manager`.
   d. Next tooling ADR: Docling library (`uv add`). Same pattern. PR via `@release-manager`.
   e. User-action: download datasets per `02-brainstorm.md` §6.2 checklist (P0: ZUGFeRD corpus + Mustang Project). Confirm P0/P1 prioritisation before continuing.
   f. First pilot data loop: generate ≈10–50 synthetic ZUGFeRD invoices via Mustang → write + ADR XML-extraction-from-PDF script → first local VLM inference on M1 Pro (Granite-Docling via MLX likely; cohort-selection ADR gates) → compute initial F1 with full rigor (v2 §5.1 + §5.5, MLflow-tracked, deterministic seed).
   g. After pilot evidence: schedule first Säring technical-progress meeting + draft agenda materials.
2. **M2D.6+** — defined at M2D.5 exit based on pilot evidence. Each architectural choice (Layer-1 spine, Layer-2 backend, Layer-3 retrieval) gated on ADR + experiment cycle per `horus-decision-discipline`. Eventually → writeup phase.

**Token economics note (2026-05-08 investigation)**: the cascade-system session that closed M2D.4 consumed ~100k+ tokens due to (a) M2D.3 bulk-authoring 46 stubs in one session, (b) reading full v2 brainstorm in chunks, (c) an M2D.4 `@grill-me` walk that was course-corrected after 5 markers (user pointed out "don't lock implementation-phase decisions at brainstorm"). Mitigation for future milestones: (1) fresh session per milestone when possible, (2) consider Sonnet 4.6 1M for code-heavy M2D.5 work (vs Opus 4.7 for architectural decisions in M2D.6+), (3) course-correct walks earlier, (4) tighter response verbosity. Queued for Cascade A horizontal sprint as `token-burn-mitigation` + `model-switching-advisory` observations in `queue/pending-review.md`.

**Original first-action (preserved for reference)**: when this handoff was authored at M2B.8, the first action was *"What's the thesis name + slug?"* and the steps were `/start-project <slug> python-ml-uv` → post-bootstrap rename → `/run-phase brainstorm`. That sequence completed at M2D.0–M2D.4.

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
