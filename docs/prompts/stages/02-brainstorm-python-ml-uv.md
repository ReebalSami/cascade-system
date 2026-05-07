# `python-ml-uv` L3 template — Brainstorm

**Phase**: brainstorm (M2B.1)
**Phase set**: cascade-system meta-repo verticals (no `phases.yaml`; M2B.x milestones = phase sequence)
**Status**: Approved (user-review gate passed 2026-05-07 per `@grill-me` step 8)
**Date**: 2026-05-07
**Milestone**: [M2B.1 — Brainstorm python-ml-uv design space](https://github.com/ReebalSami/cascade-system/issues/75)
**Handoff**: `docs/handoffs/cascade-b-template-python-ml-uv.md`
**Parent plan**: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 2 Vertical B
**Grill artifact**: this file
**Next phase**: M2B.2 — Author `~/.windsurf/templates/python-ml-uv/phases.yaml` per M1.3 contract

---

## Mission

Author a **generic Python ML / research** L3 template (`python-ml-uv`) that bootstraps any future ML project — Kaggle baselines, RL agents, vision classifiers, LLM eval harnesses, thesis/paper-companion code — from a clean-slate `/start-project python-ml-uv <slug>` invocation. The template is uv-native, reproducibility-first, and deliberately under-opinionated on contested tooling (experiment trackers, config systems, CI scaffolds).

## Scope

This brainstorm decides the **design space** for `python-ml-uv`. The template itself ships at `~/.windsurf/templates/python-ml-uv/`:

- `phases.yaml` — phase chain (B5; authored at M2B.2)
- `scaffold/` — type-specific bootstrap files (B1, B2, B3, B4, B8, B9, B10, B11; authored at M2B.3)
- `skills/` — L3 skills (B6; authored at M2B.4)
- `rules/` — L3 rules (B7; authored at M2B.5)

Cross-cutting assets (`AGENTS.md`, `docs/decisions/INDEX.md`, etc.) are NOT shipped here — they belong to `_shared/scaffold/` per ADR-004.

## Goals

- **G1** — Generic enough to bootstrap any ML / research Python project. Acceptance test: would this work for tabular Kaggle, RL agent, vision classifier, LLM eval harness, AND thesis-companion code? All five must pass.
- **G2** — Uv-native throughout. No mixed package managers (no pip / poetry / pipx / conda). uv handles Python install, dependencies, build, and run.
- **G3** — Reproducibility-first. Seeding, jupytext-paired experiments, no `.ipynb` checked in for agent-authored content.
- **G4** — Under-opinionated on contested tooling. Tracker (MLflow / W&B / TB), config-layer (Hydra / pydantic / argparse), CI scaffold all deferred to per-project decisions at consumption time.
- **G5** — Discoverable defaults. Scaffolded `README.md` documents every deferred decision with copy-paste blocks for the common choices.
- **G6** — Maintenance burden small. Loose lower-bound version pins on dev tools so `uv sync` always installs latest compatible.
- **G7** — Cascade-friendly. Rules prevent the common foot-guns (bare `python`, `pip install`, `.ipynb` editing) that break reproducibility or hit Cascade's known limitations.

## Non-goals (v1, explicit out-of-scope)

- **N1** — Cross-platform PyTorch installation (per B3). Solo + always-macOS user context. Future projects that need Linux GPU training switch to extras-based pinning at consumption time. Switch path documented in scaffolded `README.md`.
- **N2** — CI scaffold (per B10). No `.github/workflows/`. README documents how to add when needed.
- **N3** — Specific experiment tracker (per B4). Adapter is tracker-agnostic; consumer wires up at consumption time.
- **N4** — Specific config layer (per B8). Stdlib `@dataclass` placeholder ships; consumer swaps for Hydra / pydantic / argparse if desired.
- **N5** — `@tdd-ml` skill (per B6). TDD is universal, not ML-specific. Promote to L1 separately if `refs/superpowers/skills/test-driven-development/` proves valuable in Vertical D — not into L3.
- **N6** — Pyrefly / ty default (per B1). Pyrefly is Beta but pre-v1; ty is alpha (v0.0.18 per `context7`). mypy is the default; pyrefly documented as opt-in fast path.
- **N7** — `/start-project` autodetect-latest-stable Python (per B11). Captured as queue follow-up; benefits all L3 templates (cross-cutting concern), routed via `@propose-extension` → `@update-horizontal` post-M2B.1.

## Constraints

- **C1 — Generic acceptance test (handoff §9).** Every decision must pass *"would `python-ml-uv` bootstrap an unrelated ML project?"* — verified per B-question.
- **C2 — Domain-agnostic.** No thesis-specific content (no §203, ZUGFeRD, GraphRAG, document-VLM, or any specific research-domain references). Template names use generic placeholders.
- **C3 — uv-managed.** Per B7=A `uv-discipline` rule, derived projects use uv exclusively.
- **C4 — Solo + macOS user context.** Per B3 + B11, scaffold defaults to single-machine assumptions. Cross-platform is a per-project consumer change at consumption time.
- **C5 — `_shared/` boundary (ADR-004).** Cross-cutting scaffold files belong to `_shared/`; only type-specific files go in `python-ml-uv/scaffold/`.
- **C6 — L1 canonical paths (ADR-014).** Skills land at `~/.windsurf/templates/python-ml-uv/skills/<name>/SKILL.md`; rules at `~/.windsurf/templates/python-ml-uv/rules/<name>.md`.
- **C7 — Phase-taxonomy contract (M1.3).** `phases.yaml` per B5 must validate against `~/.windsurf/contracts/phase-taxonomy.md` schema. M2B.2 enforces.
- **C8 — Bundle anti-pattern enforced (ADR-018).** M2B.1–M2B.8 ship in SEPARATE PRs unless naturally bundled (handoff §3).
- **C9 — `@release-manager` discipline (ADR-018).** No naked commits to `main`. Every PR via the cluster.

## Approaches considered

One subsection per driving question (B1–B11). Full grill transcript is the chat history for this session; this artifact captures decisions, candidates, and elimination rationale. Resolution order (depth-first, dependents-resolved-first): B5 → B2 → B4 → B8 → B6 → B7 → B1 → B3 → B9 → B10 → B11.

### B5 — Phase chain shape

| # | Shape | Trade-offs |
|---|---|---|
| **A** | Keep contract example as-is: `literature → brainstorm → spec → issues → experiment → implement → writeup` (7 phases) | Most reusable — research-flavored projects (thesis, RL paper repro) and applied ML alike. Non-research consumers close `literature` trivially with a 1-paragraph "no relevant prior art" artifact. |
| **B** | Drop `literature` (6 phases) | Not every ML project needs prior-art surveying. Saves nothing material — non-research consumers pay zero for keeping `literature` in A. |
| **C** | Move `literature` after `brainstorm` (still 7) | Brainstorm scopes first, then literature targets the scope. Inverts the contract example without strong evidence the inverse is better. |

**Chosen**: **A** (contract example as-is).

Rationale:

- Contract example's `literature → brainstorm` ordering is deliberate — literature *informs* brainstorm to reduce reinventing-the-wheel.
- L2 override is cheap. Any non-research consumer who genuinely doesn't want `literature` edits their per-project `phases.yaml` once.
- Per `no-quantity-over-shape`, 7 phases isn't load — phases close trivially when there's no work; the count is shape, not load.

### B2 — Notebook discipline

| # | Shape | Trade-offs |
|---|---|---|
| **A** | jupytext + papermill, `.py` only, no `.ipynb` checked in | Diffable, reproducible, scriptable. Forces consumers off `.ipynb` for the experiment workflow. |
| **B** | Raw `.ipynb` only (`notebooks/` directory) | Lowest friction. JupyterLab + VSCode native. Default of 90% ML practitioners. |
| **C** | Hybrid: `notebooks/` for `.ipynb`, `experiments/` for jupytext-paired `.py` run via papermill | Two distinct uses get separate tooling. |

**Chosen**: **A** (jupytext + papermill, `.py` only).

Rationale:

- Reproducibility-first contract (G3). Diffable notebooks are non-negotiable for thesis-grade or production ML work.
- Agent-side `.ipynb` brittleness is a real Cascade limitation: `.ipynb` files are JSON-encoded; editing them via text-edit tools is unreliable. Plain `.py` (jupytext-paired) is infinitely cleaner for Cascade authoring + review + commit.
- Codified by B7=A `notebook-discipline` rule. Rule includes the agent-side clause explicitly: *Cascade-authored content is `.py` only; user-authored `.ipynb` allowed only by explicit user request*.

### B4 — Experiment tracking baked-in or project-choice

| # | Shape | Trade-offs |
|---|---|---|
| **A** | Bake in MLflow (default) | OSS, self-hosted. Vendor lock-in for non-MLflow consumers. |
| **B** | Bake in W&B (default) | Best-in-class UI, free academic tier. Vendor lock-in. |
| **C** | Tracker-agnostic adapter: `StdoutTracker` default + `TODO: implement <Tracker>` stub | Defers high-stakes opinionation. Each project picks at consumption. |

**Chosen**: **C** (tracker-agnostic).

Rationale:

- Acceptance test (G1): thesis → W&B; corp ML → MLflow; eval harness → stdout. C serves all three; A/B serve only one each.
- Vendor lock-in is permanent damage. Removing a wrong baked-in tracker is more friction than wiring up a chosen one.
- Adapter pattern is cheap: ~10 lines in `src/<pkg>/tracking.py` defining a `Tracker` protocol + `StdoutTracker` default. Consumer swaps `StdoutTracker` for an `MLflowTracker` / `WandBTracker` in ~15 minutes.
- B6's `@run-experiment` skill calls `tracker.log_metric(...)` agnostically, working regardless of consumer choice.

### B8 — Reproducibility primitives

| # | Shape | Trade-offs |
|---|---|---|
| **A** | Hydra + `configs/` directory | Industry-standard for academic ML. Heavyweight; janky plugin system. |
| **B** | Pydantic + manual seeding | Lightweight, ubiquitous in 2026. Mild opinionation. |
| **C** | Stdlib-only primitives: `set_global_seed(seed)` + `@dataclass Config` placeholder | Lowest opinionation. Seeding is universal; config-layer is project-flavored. |

**Chosen**: **C** (stdlib-only primitives).

Rationale:

- Consistency with B4=C — high-stakes opinionation on contested tooling defers to consumers.
- Seeding is genuinely universal: every randomized algorithm needs `random` / `numpy.random` / `torch.manual_seed` / `torch.cuda.manual_seed_all` / `PYTHONHASHSEED` / `torch.backends.cudnn.deterministic` set. Shipping this primitive is high-value, zero-vendor-lock.
- Config layer is project-flavored: thesis (Hydra), corp (pydantic), Kaggle (argparse), eval harness (CLI flags). No single layer serves all four.
- B6's `@run-experiment` skill carries the convention (*"seed before training"*) without enforcing a config layer.

### B6 — Type-specific L3 skills (M2B.4 set)

| # | Skills | Trade-offs |
|---|---|---|
| **A** | Ship 2: `@literature-review` + `@run-experiment` | Each codifies a B5 phase. `@run-experiment` consolidates B2+B4+B8 into one operational discipline. |
| **B** | Ship 3: A + `@tdd-ml` (ML-flavored TDD) | ML-flavored TDD framing (test data shape, gradient direction, reproducibility) plausible but unproven. |
| **C** | Ship 1: `@run-experiment` only | Defer `@literature-review` to lazy authoring at consumption time. |

**Chosen**: **A** (ship 2 skills).

Rationale:

- `@run-experiment` is non-negotiable: without it, B2/B4/B8 are scaffold-only decisions with nothing enforcing the discipline at runtime.
- `@literature-review` codifies the `literature` phase per B5=A. Wraps `@vault-research` (L1, ADR-024) for vault-resident prior art and adds external-source flow (arxiv / Semantic Scholar / web). Estimated ~80–100 line body, fits `@write-skill` ≤150 line norm.
- `@tdd` belongs at L1, not L3. TDD is universal; the "ML-flavored" framing is plausible but unproven. Per handoff §9: shoehorn into L3 only if domain-flavor is real. Defer until evidence emerges.
- `no-quantity-over-shape`: 2 isn't a target; each skill justifies itself by encoding a B5 phase.

### B7 — Type-specific L3 rules (M2B.5 set)

| # | Rules | Trade-offs |
|---|---|---|
| **A** | Ship 2: `notebook-discipline` + `uv-discipline` (broader scope: no-pip + always-uv-run + no-mixed-pkg-mgrs) | Hard invariants beyond skill-procedure scope. |
| **B** | Ship 1: `notebook-discipline` only | uv discipline implicit from `pyproject.toml` shape. |
| **C** | Skip M2B.5 entirely | Over-prescription guard. |

**Chosen**: **A** (ship 2 rules).

Rationale:

- `notebook-discipline` enforces B2=A. Without a written rule, a future Cascade absent-mindedly checks in `.ipynb` "for convenience" — defeats the discipline. Rule clauses: (a) `experiments/` is jupytext-paired `.py`; papermill consumes these; (b) Cascade-authored content is `.py` only; user-authored `.ipynb` allowed only by explicit user request.
- `uv-discipline` (renamed from `use-uv-not-pip` per B11 follow-on) clauses: (a) no `pip install` — use `uv add`; (b) **no bare `python script.py` — use `uv run python script.py`** (resolves project venv from cwd, immune to fresh-terminal venv-state mess); (c) no mixed package managers (poetry/pipx/conda forbidden in derived projects). Issue #79's body gets a one-line update at M2B.5 start to reflect the rename + broader scope.
- 2 rules is the floor of what genuinely codifies new constraints; deliberately rejected seeding (covered by `@run-experiment`), tracker-adapter (covered by `@run-experiment`), phase-discipline (covered by `/run-phase` L1).

### B1 — Type checker

| # | Choice | Trade-offs |
|---|---|---|
| **A** | mypy only (default) | Mature, broad plugin support, slowest. |
| **B** | mypy default + pyrefly opt-in fast path | Best-of-both. Two configs to maintain. |
| **C** | pyrefly only | Beta as of 2025-11-17 (Meta blog). Risk: edge-case bugs in Beta that mypy would catch. |

**Chosen**: **A** (mypy only).

Rationale (with current 2026 typechecker landscape verified via `context7`):

- L3 templates default to safest stable. Consumer who wants pyrefly's speed adds it in ~3 min. Consumer stuck with a pyrefly-Beta edge case pays much more.
- ML library compatibility: PyTorch, NumPy, scikit-learn, pandas, JAX have mature mypy integration. Pyrefly migration mostly works but leaves edge cases for ML-specific stubs.
- Speed isn't a crisis at ML project scale (mypy ~5–15 s on 10k LoC; pyrefly ~1–2 s). Gap matters for monorepos, not thesis/Kaggle/baseline scale.
- **`ty` rejected as default** — at v0.0.18 alpha per `context7`. Pre-1.0 signals API instability + unsettled edge cases. Revisit when ty hits Beta or v1.

### B3 — PyTorch installation pattern

Three candidates (verified against current `astral-sh/uv` PyTorch integration docs via `context7`):

| # | Pattern | Trade-offs |
|---|---|---|
| **A** | `UV_TORCH_BACKEND=auto` | Simplest scaffold. Future-proof against CUDA churn. Non-deterministic across heterogeneous OS/arch. |
| **B** | Extras-based (`cpu` + `cu130`) with `[tool.uv]` `conflicts` declaration | Explicit + reproducible. uv.lock encodes both; install resolves per-machine. |
| **C** | Platform-markers in `[tool.uv.sources]` | Middle ground. Fragile across OS/arch. |

**Chosen**: **A** (`UV_TORCH_BACKEND=auto`), per user delegation given solo+macOS context.

Rationale:

- User context (handoff §9 + B3 grill): solo dev, always macOS. No heterogeneity to plan for today.
- Simpler scaffold: `torch>=2.5` in deps; `.env.example` sets `UV_TORCH_BACKEND=auto`. Zero `[tool.uv.sources]` block needed.
- Future-proof: uv auto-detects at install-time. CUDA version churn doesn't require template republishing.
- Consumer who later moves training to a Linux GPU cluster switches that project's `pyproject.toml` to extras-based (B-shape) at consumption time. ~25-line addition. Documented in scaffolded README.

### B9 — Build backend

| # | Choice | Trade-offs |
|---|---|---|
| **A** | `uv_build` | uv-native; ~3-line `[build-system]`. |
| **B** | `hatchling` | Mature, plugin-rich. Extra mental model. |
| **C** | `setuptools` | Legacy, broadest plugins. Heaviest. |

**Chosen**: **A** (`uv_build`).

Rationale:

- Aligned with `uv-discipline` rule (B7=A). Picking hatchling here would be a soft contradiction.
- ML projects rarely need exotic build steps. Pure Python with optional Cython fits `uv_build`'s scope.
- uv_build matured significantly in 2026: handles editable installs, PEP 517/518/621 compliance.
- Switching to hatchling/setuptools later if a project genuinely needs custom build steps is a ~5-line edit.

### B10 — CI scaffold

| # | Choice | Trade-offs |
|---|---|---|
| **A** | Ship `.github/workflows/ci.yml` with `setup-uv@v3` + `make check` | Out-of-the-box CI. Imposes delete on no-CI consumers. |
| **B** | Ship `ci.yml.example`; consumer renames to activate | Halfway. Adds a friction step. |
| **C** | Don't ship CI; README documents how to add (15-line copy-paste block) | Lean. Aligned with cascade-system's "no CI by default" pattern. |

**Chosen**: **C** (don't ship CI).

Rationale:

- User's established pattern: cascade-system meta-repo has no CI by deliberate design (Sprint 1.5 retro). Defaulting derived projects to A imposes delete-step on most.
- Asymmetric cost favors C: A imposes ~10 sec deletion every project; C imposes ~2 min add when CI actually wanted (occasional).
- README-documented copy-paste block is discoverable; no friction; no automatic imposition.
- Vertical D consideration: thesis-flavored projects sometimes want CI (typecheck-on-PR), but a thesis often doesn't have collaborators in the relevant time window. If wanted, add the 15-line block.

### B11 — Python version pinning + `.venv` posture

`.venv/` per project: not a brainstorm decision — uv's default behavior. Scaffold's `.gitignore` includes `.venv/`. Confirmed.

Three pinning strategies:

| # | Strategy | Trade-offs |
|---|---|---|
| **A** | Pin to specific minor (`requires-python = ">=3.14"` + `.python-version: 3.14`) | Simple. Consumer gets known-good version. Template stales over time. |
| **B** | Range pin (`>=3.13,<3.16`) | Flexible. Doesn't force latest. |
| **C** | Autodetect-at-scaffold via `/start-project` enhancement | Always latest. Requires L1 workflow change. |

**Chosen**: **A** (pin to `>=3.14` + `.python-version: 3.14`).

Rationale:

- A is in-scope for M2B.1; C is L1 workflow change (would block vertical on `@propose-extension`).
- A satisfies user instruction *today*: machine on 3.14.3 = current global latest stable (3.15 is alpha per `uv python list`).
- B is too loose: a fresh consumer machine with 3.13 silently uses 3.13, missing 3.14 features (e.g., t-strings PEP 750).
- **C captured as queue follow-up** at `cascade-system/queue/pending-review.md` (post-M2B.1 PR). Benefits ALL L3 templates (Verticals C/D/E/F too); routed via `@propose-extension` → `@update-horizontal` per ADR-017.

## Acceptance test (handoff §9 generic-ML check)

Verification that each locked decision passes *"would `python-ml-uv` bootstrap an unrelated ML project (Kaggle tabular baseline OR LLM-eval harness)?"*:

| Decision | Kaggle tabular | LLM-eval harness | Pass |
|---|---|---|---|
| B5=A phase chain | `literature` closes with "no prior art"; `experiment` is load-bearing | `literature` reviews benchmark prior art; `experiment` runs eval | ✓ |
| B2=A jupytext+papermill | `experiments/baseline.py` runs the model | `experiments/eval_run.py` runs the eval | ✓ |
| B4=C tracker-agnostic | `StdoutTracker` for prints; consumer wires Optuna | Logs JSON to file via custom adapter | ✓ |
| B8=C stdlib seeding | `set_global_seed(42)` ensures fold-split reproducibility | `set_global_seed(42)` ensures eval determinism | ✓ |
| B6=A skills | `@literature-review` + `@run-experiment` apply | Same | ✓ |
| B7=A rules | uv-managed; jupytext-only experiments | Same | ✓ |
| B1=A mypy | Tabular code well-typed (pandas + sklearn) | Eval code typed (json schemas + LLM clients) | ✓ |
| B3=A `UV_TORCH_BACKEND=auto` | macOS auto-detects mps; works for CPU pandas/sklearn (torch optional dep) | Same | ✓ |
| B9=A uv_build | Pure-Python | Pure-Python | ✓ |
| B10=C no CI | User adds if/when wanted | User adds if/when wanted | ✓ |
| B11=A `>=3.14` | 3.14 features (t-strings PEP 750) usable | Same | ✓ |

✓ All decisions pass §9.

## Maintenance principles

The following principles inform the scaffolded `pyproject.toml` and ongoing template maintenance:

- **Tool dev-deps use loose lower-bound pinning**: `mypy>=1.13`, `ruff>=0.7`, `pytest>=8`, `jupytext>=1.16`, `papermill>=2.6` (exact version numbers updated to current latest stable at M2B.3 scaffold-authoring time, verified via `context7`). uv.lock encodes exact versions per consumer; `uv sync --upgrade` refreshes.
- **Library deps follow same pattern** when added by consumer: lower-bound only (e.g., `torch>=2.5`, `numpy>=2.0`).
- **No `==` pins anywhere in `pyproject.toml`** — that's `uv.lock`'s job.
- **Template maintenance commits** to `~/.windsurf/templates/python-ml-uv/scaffold/pyproject.toml` happen only when significant new minimums emerge (e.g., when `mypy 2.0` ships with breaking config changes, or Python 3.15 reaches stable).
- **Python version pinning evolves** when next-stable hits: M2B.1 pins `>=3.14`; the day Python 3.15 reaches stable, update the template via a chore PR.
- **Cascade-D handoff** (M2B.8) inherits this maintenance posture for thesis-derived projects.

## Carry-forward to downstream M2B.x milestones

| Milestone | Carries from M2B.1 |
|---|---|
| **M2B.2** (`phases.yaml`) | B5=A → 7-phase chain. M2B.2 maps each phase to a `skill:` field per the M1.3 contract: `literature` → `@literature-review` (M2B.4), `brainstorm` → `@grill-me` (L1), `spec` → `@to-prd` (L1), `issues` → `@to-issues` (L1), `experiment` → `@run-experiment` (M2B.4), `implement` → TBD (L1 default OR M2B.2 question), `writeup` → TBD (M2B.2 question). |
| **M2B.3** (scaffold) | B1=A (mypy + plugins for pydantic/torch/numpy in `[tool.mypy]`); B2=A (jupytext + papermill in dev deps; `experiments/.gitkeep`; `Makefile` `make experiment NB=experiments/<name>.py`); B3=A (`UV_TORCH_BACKEND=auto` in `.env.example`; `torch>=2.5` in deps); B4=C (`src/<pkg>/tracking.py` adapter with `Tracker` Protocol + `StdoutTracker` default + `TODO: implement <Tracker>` stub); B8=C (`src/<pkg>/seeding.py` with `set_global_seed`; `src/<pkg>/config.py` with `@dataclass Config` placeholder); B9=A (`[build-system] build-backend = "uv_build"`); B10=C (no `.github/`; README §`Adding CI` with copy-paste block); B11=A (`requires-python = ">=3.14"`; `.python-version: 3.14`). Plus minimal `Makefile` (no `make typecheck-fast`, no `make mlflow-ui`); `.gitignore` per `gitignore.io --python` template + `.venv/` + `*.ipynb_checkpoints/`. |
| **M2B.4** (skills) | B6=A → author `@literature-review` + `@run-experiment` via `@write-skill` per ADR-006. Skills land at `~/.windsurf/templates/python-ml-uv/skills/<name>/SKILL.md`. |
| **M2B.5** (rules) | B7=A → author `notebook-discipline` + `uv-discipline` (renamed from `use-uv-not-pip` per B11 follow-on). **Update issue #79 body at M2B.5 start to reflect rename + broader scope (uv-run + no-pip + no-mixed-pkg-mgrs).** Rules land at `~/.windsurf/templates/python-ml-uv/rules/<name>.md`. |
| **M2B.6** (`/start-project --dry-run` validation) | Full integration test. Run `/start-project --dry-run python-ml-uv-test python-ml-uv` from a clean Cascade session; verify scaffold materializes; phases.yaml validates against M1.3 contract; skills + rules deploy to project's `.windsurf/`; `uv sync` succeeds; `uv run pytest` runs the smoke test. |
| **M2B.7** (per-vertical retro) | Each B-decision verified or revised. Update `retros/sprint-2-vertical-b.md`. |
| **M2B.8** (Cascade-D handoff) | python-ml-uv now bootstrap-able; thesis-name + slug = D's first decisions. |

## Open / deferred items

Items deliberately deferred from M2B.1 to avoid scope creep:

- **`/start-project` autodetect-latest-stable enhancement** (B11 follow-on). Captured to `cascade-system/queue/pending-review.md` post-M2B.1-PR. Cross-cutting (benefits all L3 templates); routed via `@propose-extension` → `@update-horizontal` per ADR-017.
- **`@tdd` skill placement** (B6 follow-on). If `refs/superpowers/skills/test-driven-development/SKILL.md` proves valuable in Vertical D experiments, promote to L1 separately. Capture in queue when first surfaced.
- **`implement` and `writeup` phase skill mappings** (M2B.2 question). M2B.2 decides whether each gets a dedicated L3 skill, an L1 default (e.g., `@executing-plans` from superpowers), or `skill: <generic>` if the contract allows.
- **`pyrefly` opt-in fast path** (B1 follow-on). Scaffolded README documents how to add `pyrefly` as a dev-dep + Makefile target if speed becomes a concern.
- **Cross-platform PyTorch (extras-based)** (B3 follow-on). Scaffolded README documents the switch path for projects that move training to Linux GPU clusters.

## Next phase

**M2B.2 — Author `phases.yaml`** at `~/.windsurf/templates/python-ml-uv/phases.yaml` per the M1.3 phase-taxonomy contract. Inputs: this brainstorm artifact (specifically B5=A) + the contract schema. Output: a validated `phases.yaml` with 7 phases (literature → brainstorm → spec → issues → experiment → implement → writeup), each mapped to a `skill:` field per the carry-forward table above.

M2B.2 transition is user-explicit per `@grill-me` step 9 — not auto-invoked. User opens M2B.2 when ready, typically after M2B.1 PR merges.

## Provenance

- **Grill session**: `@kickoff` → plan `~/.windsurf/plans/cascade-b-orient-41859d.md` → `@grill-me` M2B.1 B1–B11 (2026-05-07 interactive session)
- **Handoff**: `docs/handoffs/cascade-b-template-python-ml-uv.md`
- **Parent plan**: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 2 Vertical B
- **Predecessor shape**: `docs/prompts/stages/m_c2-1-brainstorm.md` (Vertical C2 brainstorm — adapted for shape)
- **Related ADRs cited during grill**:
  - ADR-003 (strict-docs-structure)
  - ADR-004 (`_shared/scaffold/` two-pass)
  - ADR-006 (SKILL.md frontmatter schema)
  - ADR-009 (NNN reserve-in-INDEX-first)
  - ADR-011 (supersession over deletion)
  - ADR-014 (L1 canonical storage paths)
  - ADR-015 (`@<skill>` vs `/<workflow>` syntax)
  - ADR-017 (`@propose-extension` intake)
  - ADR-018 (release-discipline cluster)
  - ADR-019 (`@begin` entry skill)
  - ADR-020 (`@kickoff` entry skill)
  - ADR-022 (Obsidian CLI selection)
  - ADR-024 (`@vault-research` skill — `@literature-review` design reference)
- **L1 skills consulted**: `~/.codeium/windsurf/skills/grill-me/SKILL.md` (this skill — step 6 artifact shape), `~/.codeium/windsurf/skills/kickoff/SKILL.md` (orientation procedure), `~/.codeium/windsurf/skills/write-skill/SKILL.md` (M2B.4 author-time reference), `~/.codeium/windsurf/skills/vault-research/SKILL.md` (B6 `@literature-review` design reference)
- **L1 contracts consulted**: `~/.windsurf/contracts/phase-taxonomy.md` (M1.3 `phases.yaml` schema for B5/M2B.2)
- **`context7` queries consulted**:
  - `/astral-sh/ty` (B1 — verified ty at v0.0.18 alpha; rejected as default)
  - `/facebook/pyrefly` (B1 — verified pyrefly at Beta as of 2025-11-17 Meta blog)
  - `/astral-sh/uv` (B3 — verified canonical PyTorch integration patterns: `UV_TORCH_BACKEND=auto`, extras-based with `conflicts`, platform-markers)
- **Reference vendoring (per `adapt-from-all` rule)**: `refs/superpowers/skills/test-driven-development/SKILL.md` (B6 — read-only reference; `@tdd` deferred to L1)
- **User context**: solo dev, always-macOS (per handoff §9 + B3/B11 grill). Machine: Python 3.14.3 (Homebrew) + 3.13.9 (pyenv shim) + uv 0.9.8 (Homebrew, 2025-11-07).
