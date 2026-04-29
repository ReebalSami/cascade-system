# Handoff: Cascade B — `python-ml-uv` L3 template (Sprint 2)

**From**: Cascade A (horizontal, Sprint 1 closer)
**To**: Cascade B (vertical, owns the `python-ml-uv` L3 project-type template)
**Sprint**: 2 (Vertical B)
**Plan ref**: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 2 (M2B.1 – M2B.7)

You are a fresh Cascade picking up the meta-system's first L3 template authoring. Sprint 1 (L1 Foundation) is complete; this handoff is **self-contained** — disk + GitHub state are the only inputs you need.

## 1 — Your role

You author the `python-ml-uv` L3 project-type template at `~/.windsurf/templates/python-ml-uv/`. This template will be consumed by `/start-project` to bootstrap any future Python ML research project (including the upcoming thesis project, Cascade D).

You do **not** modify L1 (`~/.windsurf/`). If a gap is found in L1 during your work, log it via `bidirectional-learning-pipe` to `~/Projects/cascade-system/queue/pending-review.md` for Sprint 2's `/sprint-review` to triage.

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
| 7 | `~/.windsurf/rules/*.md` | All L1 rules (you operate under these) |
| 8 | `~/.windsurf/skills/grill-me/SKILL.md` | Reference SKILL.md per ADR-006 |
| 9 | `~/.windsurf/workflows/start-project.md` | The consumer of your template |
| 10 | `~/.windsurf/workflows/add-project-type.md` | Use this workflow to author your template |
| 11 | `~/.windsurf/templates/_shared/scaffold/` | The shared scaffold your type-specific scaffold will be layered on top of (do not duplicate) |

## 3 — Work to do (Sprint 2 Vertical B)

Per plan §4 Sprint 2:

- **M2B.1** — Run `/grill-me` on the python-ml-uv design space. What's the canonical phase chain? What domain-specific skills are needed? What scaffold files are stack-specific? Output: brainstorm artifact at `~/Projects/cascade-system/docs/prompts/stages/02-brainstorm-python-ml-uv.md` (meta-repo's own brainstorm for the template).

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

- **M2B.4** — Author type-specific L3 skills as needed (likely `/literature-review`, `/run-experiment`, possibly `/tdd` if it's domain-flavored enough to warrant L3 vs L1). Each via `/write-skill` per ADR-006. Skills go in `~/.windsurf/templates/python-ml-uv/skills/`.

- **M2B.5** — Author type-specific rules as needed (e.g., `notebook-discipline.md` if cells-vs-modules pattern is worth codifying). Skip if no clear rule emerges. Rules go in `~/.windsurf/templates/python-ml-uv/rules/`.

- **M2B.6** — Validate the template via `/start-project --dry-run python-ml-uv-test python-ml-uv`. Fix until clean.

- **M2B.7** — Write a vertical retro at `~/Projects/cascade-system/retros/sprint-2-vertical-b.md` (note: this retro is per-vertical, not per-sprint, since both verticals B and C are in Sprint 2). Format per `~/.windsurf/templates/_shared/retro-template.md`.

Each milestone closes its corresponding GitHub issue (filed during Sprint 0 M0.6 — check Project board for the M2B.x issues).

## 4 — Operating constraints

You operate under all L1 rules in `~/.windsurf/rules/`. Highlights to internalize:

- **`adapt-from-all`**: every authored skill/rule/contract carries `sources_consulted` + `adapted_for` frontmatter. Consult `refs/superpowers/`, `refs/mattpocock-skills/`, `refs/claude-skills/`, `refs/awesome-agent-skills/` first.
- **`document-as-you-go`**: every significant decision becomes an ADR at the moment of decision. ADRs go in `~/Projects/cascade-system/docs/decisions/` (meta-repo, not project-local — this work is meta-repo-shaping). Number = next available; check `INDEX.md` first to avoid the collision pattern Sprint 1 hit.
- **`no-time-estimates`**: phrase progress in event-based language ("after the smoke test passes", not "in two days").
- **`no-quantity-over-shape`**: describe shape, not counts.
- **`no-terminal-oneline-scripts`**: heredocs banned; use `write_to_file` / `edit` for content delivery.
- **`make-sure-it-works`**: every milestone closes only after its DoD is verified. For your work, "verified" = `/start-project --dry-run python-ml-uv-test python-ml-uv` produces a clean repo + `pyproject.toml` parses + test smoke runs.
- **`bidirectional-learning-pipe`**: capture friction/insights to the meta-repo queue as they appear.
- **`plan-drift-watcher`**: surface drift inline if observed.
- **`sprint-review-prompt`**: when a milestone closes, suggest `/sprint-review` (the user opens it).

ADR routing per ADR-001:
- Issue close + comment → MCP (`mcp3_add_issue_comment` + `mcp3_issue_write`)
- Repo / Project v2 mutations → `gh` CLI with `GITHUB_TOKEN= GH_TOKEN=` token-bypass

## 5 — Useful commands

| Command | Purpose |
|---|---|
| `mcp3_list_issues owner=ReebalSami repo=cascade-system labels=[vertical-b]` | List your open issues |
| `mcp3_issue_read method=get owner=... repo=... issue_number=N` | Read issue body |
| `gh project list --owner ReebalSami` | Find the cascade-system Project v2 |
| `/grill-me` | M2B.1 |
| `/write-skill` | M2B.4 (per type-specific skill) |
| `/start-project --dry-run python-ml-uv-test python-ml-uv` | M2B.6 validation |

## 6 — Pitfalls to avoid (carried from Sprint 1 friction)

- **Don't put `docs/*` in `<type>/scaffold/`** — duplicates `_shared/`; violates ADR-004. Type-specific = code/build/test only.
- **Don't author type-specific skills as L1** — they go in `~/.windsurf/templates/python-ml-uv/skills/`, not `~/.windsurf/skills/`. L1 is owned by Cascade A.
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

> Vertical B (`python-ml-uv` L3 template) complete. Retro at `retros/sprint-2-vertical-b.md`. Cascade D (thesis) can now run `/start-project <thesis-name> python-ml-uv` to bootstrap from this template. L1 observations queued for Cascade A's next horizontal `/sprint-review`.

Do **not** start Cascade D's work yourself. That's a separate vertical with its own handoff.

## 9 — Provenance

This handoff is the sprint-1-to-sprint-2 vertical-spawn for Vertical B. Pattern derived from `cascade-system/docs/decisions/ADR-002-handoff-prompts-subdirectory.md` and `cascade-a-sprint-1.md` (the precedent handoff).

Sprint 1 retro: `retros/sprint-1.md`.
Plan: `~/.windsurf/plans/cascade-project-system-cac5f9.md` §4 Sprint 2 Vertical B.
