# Handoff: Cascade B — `python-ml-uv` L3 template (Sprint 2)

**From**: Cascade A (horizontal, Sprint 1 closer)
**To**: Cascade B (vertical, owns the `python-ml-uv` L3 project-type template)
**Sprint**: 2 (Vertical B)
**Plan ref**: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 2 (M2B.1 – M2B.7)

You are a fresh Cascade picking up the meta-system's first L3 template authoring. Sprint 1 (L1 Foundation) is complete; this handoff is **self-contained** — disk + GitHub state are the only inputs you need.

> **Note on amendment (added pre-vertical-B kickoff)**: Vertical B was queued at end-of-Sprint-1 but bypassed in favor of Vertical C / C2 (Obsidian work). When `@kickoff` opened this handoff, Sprint 1.5 + Vertical C + Vertical C2 had shipped, adding ADRs 014–031, the cheat-sheet / manual / system-overview-diagram, the `@begin` / `@kickoff` / `@verify-l1` / `@vault-research` / `@vault-distill` / `@release-manager` / `@propose-extension` skills, the `/commit` and `/branch-*` workflows, and L1 storage canonicalization (ADR-014). This handoff has been **amended in place** (per ADR-011 supersession-over-deletion: original semantics preserved; gaps filled): §2 read-list extended, §4 constraints extended with post-Sprint-1.5 idioms, §3 M2B.3 + M2B.4 annotated with modern uv + adapt-from-all guidance, §3 milestone-filing note corrected. Substantive scope (M2B.1 → M2B.7 work breakdown) is unchanged. The "self-contained" claim above remains true *for the disk + GitHub state at amendment time* — but the read-list (§2) is the authoritative orientation, and reading only the original Sprint-1-era pointers would skip post-Sprint-1.5 system surface that Vertical B operates against. Amendment plan: `~/.windsurf/plans/cascade-b-kickoff-6bbb3c.md`.

## 1 — Your role

You author the `python-ml-uv` L3 project-type template at `~/.windsurf/templates/python-ml-uv/`. This template will be consumed by `/start-project` to bootstrap any future Python ML research project (including the upcoming thesis project, Cascade D).

You do **not** modify L1 (`~/.windsurf/`). If a gap is found in L1 during your work, log it via `bidirectional-learning-pipe` to `~/Projects/cascade-system/queue/pending-review.md` for Sprint 2's `@sprint-review` to triage.

You are vertical, not horizontal. Stay in scope.

## 2 — Read these in order before doing anything

| # | Path | Why |
|---|---|---|
| 1 | `~/Projects/cascade-system/README.md` | Top-level orientation |
| 2 | `~/Projects/cascade-system/docs/structure.md` | Meta-repo conventions |
| 3 | `~/Projects/cascade-system/retros/sprint-1.md` | What just shipped + open observations |
| 4 | `~/Projects/cascade-system/docs/decisions/INDEX.md` | All ADRs |
| 5 | `~/Projects/cascade-system/docs/decisions/ADR-004-shared-scaffold-pattern.md` | **Critical**: how your `<type>/scaffold/` interacts with `_shared/scaffold/` |
| 6 | `~/.windsurf/contracts/phase-taxonomy.md` | Your `phases.yaml` must conform to v1 |
| 7 | `~/Projects/cascade-system/docs/rules/*.md` | All L1 rules (you operate under these) |
| 8 | `~/.codeium/windsurf/skills/grill-me/SKILL.md` | Reference SKILL.md per ADR-006 |
| 9 | `~/.codeium/windsurf/global_workflows/start-project.md` | The consumer of your template |
| 10 | `~/.codeium/windsurf/global_workflows/add-project-type.md` | Use this workflow to author your template |
| 11 | `~/.windsurf/templates/_shared/scaffold/` | The shared scaffold your type-specific scaffold will be layered on top of (do not duplicate) |
| 12 | `~/Projects/cascade-system/docs/cheat-sheet.md` | Single-page scannable inventory of every skill / workflow / rule / contract / template (Sprint 1.5.3) |
| 13 | `~/Projects/cascade-system/docs/manual.md` | Narrative system manual (mental model, lifecycle, release discipline) (Sprint 1.5.10) |
| 14 | `~/Projects/cascade-system/docs/architecture/system-overview.mmd` | Layered architecture diagram (Sprint 1.5.5) |
| 15 | `~/Projects/cascade-system/retros/sprint-1-5.md` | Onboard ramp retro — `@begin`, `@kickoff`, cheat-sheet, manual landed |
| 16 | `~/Projects/cascade-system/retros/sprint-2-vertical-c.md` | Vertical C (Obsidian) retro — patterns useful for L3 skill authoring (M2B.4) |
| 17 | `~/Projects/cascade-system/retros/sprint-3-c2-write-path.md` | Vertical C2 (vault write-path) retro — `@vault-distill`, ADR-030/031 |
| 18 | `~/Projects/cascade-system/docs/decisions/ADR-014-l1-canonical-storage-paths.md` | L1 surface lives at `~/.codeium/windsurf/...`; templates + contracts at `~/.windsurf/...` |
| 19 | `~/Projects/cascade-system/docs/decisions/ADR-015-skill-vs-workflow-invocation-syntax.md` | `@<skill>` vs `/<workflow>` — used by `phases.yaml` `skill:` field |
| 20 | `~/Projects/cascade-system/docs/decisions/ADR-017-propose-extension-intake-channel.md` | `@propose-extension` is the single intake channel for any system extension |
| 21 | `~/Projects/cascade-system/docs/decisions/ADR-018-release-discipline-cluster.md` | `@release-manager` orchestrates branch → PR → CI → squash-merge for every `main`-bound change |
| 22 | `~/.codeium/windsurf/skills/verify-l1/SKILL.md` | Use `@verify-l1` to validate L1 layout after authoring template artifacts |

## 3 — Work to do (Sprint 2 Vertical B)

Per plan §4 Sprint 2:

- **M2B.1** — Run `@grill-me` on the python-ml-uv design space. What's the canonical phase chain? What domain-specific skills are needed? What scaffold files are stack-specific? Output: brainstorm artifact at `~/Projects/cascade-system/docs/prompts/stages/02-brainstorm-python-ml-uv.md` (meta-repo's own brainstorm for the template).

- **M2B.2** — Author `~/.windsurf/templates/python-ml-uv/phases.yaml`. Conforms to M1.3 contract v1. The phase set illustrated in the contract (`literature → brainstorm → spec → issues → experiment → implement → writeup`) is a *reference*, not a mandate — adjust based on M2B.1 brainstorm.

- **M2B.3** — Author `~/.windsurf/templates/python-ml-uv/scaffold/` with **type-specific files only** (per ADR-004; `docs/` content lives in `_shared/scaffold/`). Initial set:
  - `pyproject.toml` (uv-managed, with pytest + ruff + mypy dev deps)
  - `Makefile` (targets: `make install`, `make test`, `make lint`, `make typecheck`, `make notebook`, `make clean`)
  - `src/<pkg>/__init__.py`
  - `tests/__init__.py` and `tests/test_smoke.py`
  - `notebooks/.gitkeep`
  - `.python-version`
  - `.gitignore` (Python + ML + uv flavored)
  - `README.md` (scaffolded; will be customized post-`/start-project`)

  **Amendment note for M2B.3** (post-handoff uv evolution): the M2B.1 brainstorm should grill these modern-tooling choices that did not exist when the handoff was authored:
  - **PEP 735 `[dependency-groups]`** is the official-standard way to declare dev deps (separate from `[project.optional-dependencies]` which is for end-user-visible extras). uv supports nested groups via `include-group`. The original "uv-managed pyproject.toml with pytest + ruff + mypy dev deps" line should resolve as `[dependency-groups] dev = ["pytest>=...", "ruff>=...", "mypy>=..."]` per `context7 /astral-sh/uv` — verify against latest `uv` docs at template-authoring time.
  - **PyTorch first-class support**: `tool.uv.sources` with platform markers OR conflicting extras (`cpu` / `gpu`) selects the correct PyTorch wheel index; also `UV_TORCH_BACKEND=auto` autodetects GPU. Load-bearing for ML projects. Consult `https://docs.astral.sh/uv/guides/integration/pytorch/` at authoring time.
  - **Type checker landscape shift**: `mypy` is no longer the only mainstream choice — `ty` (Astral, Rust-based, 10–60× faster, alpha) and `pyrefly` (Meta, alpha) compete. The 2026 reference template `cookiecutter-uv` ships *both* `mypy` and `ty`. Brainstorm decides: mypy-only / ty-only / both. Defer the choice; document the chosen rationale in M2B.1's brainstorm artifact.
  - **`uv_build`** is uv's own build backend — replaces hatchling/setuptools for simple packages. Optional; defer to brainstorm.
  - **`deptry`** (dead-import / dependency-leak detector) — newer than the handoff's pytest+ruff+mypy trio; consider adding to `[dependency-groups]` and `make check`.
  - **Notebook discipline**: `jupytext` (write `.py` for git, convert to `.ipynb` for execution) + `papermill` (parameterized execution) is the dominant 2026 pattern; informs M2B.5's `notebook-discipline` rule decision.

- **M2B.4** — Author type-specific L3 skills as needed (likely `@literature-review`, `@run-experiment`, possibly `@tdd` if it's domain-flavored enough to warrant L3 vs L1). Each via `@write-skill` per ADR-006. Skills go in `~/.windsurf/templates/python-ml-uv/skills/`.

  **Amendment note for M2B.4**: per `adapt-from-all`, before authoring each skill, consult the vendored references at `refs/superpowers/`, `refs/mattpocock-skills/`, `refs/claude-skills/`, `refs/awesome-agent-skills/`. Specifically:
  - For `@tdd`: `refs/superpowers/skills/test-driven-development/` is the canonical adapt-from source (red → green → refactor with pytest).
  - For `@literature-review`: no clear upstream; synthesize from research-discipline patterns + (optionally) the user's own thesis context via `@vault-research` (vault has 26 thesis hits as of amendment time).
  - For `@run-experiment`: consult `refs/` for any experiment-runner skill; else synthesize from notebook-discipline + reproducibility primitives.
  - Each authored skill carries `sources_consulted` + `adapted_for` frontmatter per ADR-006 schema. Each skill's authoring lands as a separate PR via `@release-manager`.

- **M2B.5** — Author type-specific rules as needed (e.g., `notebook-discipline.md` if cells-vs-modules pattern is worth codifying). Skip if no clear rule emerges. Rules go in `~/.windsurf/templates/python-ml-uv/rules/`.

- **M2B.6** — Validate the template via `/start-project --dry-run python-ml-uv-test python-ml-uv`. Fix until clean.

- **M2B.7** — Write a vertical retro at `~/Projects/cascade-system/retros/sprint-2-vertical-b.md` (note: this retro is per-vertical, not per-sprint, since both verticals B and C are in Sprint 2). Format per `~/.windsurf/templates/_shared/retro-template.md`.

Each milestone closes its corresponding GitHub issue. **Note (amendment)**: Sprint 0 M0.6 did not actually file the M2B.x issues — Vertical C went first, then C2; B was bypassed. The 7 M2B.x issues + milestones are filed at vertical-start by `@kickoff` step 6 (see amendment note above). Check the Project board after the kickoff dispatch to confirm.

## 4 — Operating constraints

You operate under all L1 rules in `~/Projects/cascade-system/docs/rules/`. Highlights to internalize:

- **`adapt-from-all`**: every authored skill/rule/contract carries `sources_consulted` + `adapted_for` frontmatter. Consult `refs/superpowers/`, `refs/mattpocock-skills/`, `refs/claude-skills/`, `refs/awesome-agent-skills/` first.
- **`document-as-you-go`**: every significant decision becomes an ADR at the moment of decision. ADRs go in `~/Projects/cascade-system/docs/decisions/` (meta-repo, not project-local — this work is meta-repo-shaping). Number = next available; check `INDEX.md` first to avoid the collision pattern Sprint 1 hit.
- **`no-time-estimates`**: phrase progress in event-based language ("after the smoke test passes", not "in two days").
- **`no-quantity-over-shape`**: describe shape, not counts.
- **`no-terminal-oneline-scripts`**: heredocs banned; use `write_to_file` / `edit` for content delivery.
- **`make-sure-it-works`**: every milestone closes only after its DoD is verified. For your work, "verified" = `/start-project --dry-run python-ml-uv-test python-ml-uv` produces a clean repo + `pyproject.toml` parses + test smoke runs.
- **`bidirectional-learning-pipe`**: capture friction/insights to the meta-repo queue as they appear.
- **`plan-drift-watcher`**: surface drift inline if observed.
- **`sprint-review-prompt`**: when a milestone closes, suggest `@sprint-review` (the user opens it).

Post-Sprint-1.5 idioms (added at amendment time):

- **`@release-manager` (ADR-018)**: every `main`-bound change lands via `@release-manager` (branch → commits → push → PR → CI → squash-merge → cleanup). NEVER `git push origin main` directly. Helper workflows: `/branch-start`, `/branch-push-and-pr`, `/ci-watch`, `/branch-merge-and-cleanup`.
- **`/commit` (ADR-013)**: for any commit with multi-paragraph message body. Single-line `git commit -m "subject"` is OK for subject-only commits. Embedded newlines in `-m` arguments WILL CRASH the macOS-Windsurf integrated terminal (rule `no-terminal-oneline-scripts`).
- **`@propose-extension` (ADR-017)**: if you observe an L1 gap that warrants a build (rather than just capture), do NOT author L1 directly — route via `@propose-extension` which dispatches to `@write-skill` / `@update-horizontal` / direct-write per artifact type. Capture-only friction still goes to `bidirectional-learning-pipe` queue per the bullet above.
- **`@verify-l1` (ADR-029)**: after authoring template artifacts, run `@verify-l1` to validate L1 layout against ADR-014 + path-drift sweep. Catches dead `~/.windsurf/skills/` / `~/.windsurf/workflows/` / `~/.windsurf/rules/` references in template files.
- **`@vault-research` (ADR-024)**: when authoring skills that benefit from user-vault context (e.g., `@literature-review` for the thesis-domain corpus), use `@vault-research`. Vault has 26 thesis hits + 10 ML hits as of amendment time; thesis-precursor courses (DL, GNN, Safe RL) are in `originals/academic/masters/courses/`. Use `obsidian` CLI per ADR-022 (filesystem-direct reads on iCloud-symlinked vault hit ghost-empty failures).

ADR routing per ADR-001:
- Issue close + comment → MCP (`mcp3_add_issue_comment` + `mcp3_issue_write`)
- Repo / Project v2 mutations → `gh` CLI with `GITHUB_TOKEN= GH_TOKEN=` token-bypass

## 5 — Useful commands

| Command | Purpose |
|---|---|
| `mcp3_list_issues owner=ReebalSami repo=cascade-system labels=[vertical-b]` | List your open issues |
| `mcp3_issue_read method=get owner=... repo=... issue_number=N` | Read issue body |
| `gh project list --owner ReebalSami` | Find the cascade-system Project v2 |
| `@grill-me` | M2B.1 |
| `@write-skill` | M2B.4 (per type-specific skill) |
| `/start-project --dry-run python-ml-uv-test python-ml-uv` | M2B.6 validation |

## 6 — Pitfalls to avoid (carried from Sprint 1 friction)

- **Don't put `docs/*` in `<type>/scaffold/`** — duplicates `_shared/`; violates ADR-004. Type-specific = code/build/test only.
- **Don't author type-specific skills as L1** — they go in `~/.windsurf/templates/python-ml-uv/skills/`, not `~/.codeium/windsurf/skills/`. L1 is owned by Cascade A.
- **Don't author placeholder-only stubs** — `pyproject.toml` should compile, the smoke test should run. First real use of the template (Cascade D's thesis bootstrap) shouldn't be the validation step.
- **Don't bundle ADR-numbering** — claim a number by writing the file; if a collision happens (per Sprint 1 ADR-004 incident), renumber yours and add a `Note on numbering` line. No drama.
- **Don't drift into L1 work** — if you spot an L1 gap, log to queue. Don't fix it. Cascade A handles L1 in horizontal sprints.

## 7 — Definition of done

You're finished when:

- All M2B.x issues are closed (verify in Project board)
- The template is validated via `/start-project --dry-run`
- Sprint 2 vertical B retro is written and `Status: closed`
- Any L1 observations are in `~/Projects/cascade-system/queue/pending-review.md`
- Final commit pushed to the cascade-system repo with a clean working tree

## 8 — Termination + handoff

When done, your last action is:

> Vertical B (`python-ml-uv` L3 template) complete. Retro at `retros/sprint-2-vertical-b.md`. Cascade D (thesis) can now run `/start-project <thesis-name> python-ml-uv` to bootstrap from this template. L1 observations queued for Cascade A's next horizontal `@sprint-review`.

Do **not** start Cascade D's work yourself. That's a separate vertical with its own handoff.

## 9 — Provenance

This handoff is the sprint-1-to-sprint-2 vertical-spawn for Vertical B. Pattern derived from `cascade-system/docs/decisions/ADR-002-handoff-prompts-subdirectory.md` and `cascade-a-sprint-1.md` (the precedent handoff).

Sprint 1 retro: `retros/sprint-1.md`.
Plan: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 2 Vertical B.
