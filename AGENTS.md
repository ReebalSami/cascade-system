# AGENTS.md — cascade-system meta-repo

This is **cascade-system** — the L1 meta-repository for the Cascade personal system. Artifacts authored here govern every project under `~/Projects/`.

## Start here

- `docs/manual.md` — system manual (mental model, invocation cheat-sheet, release discipline, full "where things live" reference)
- `docs/decisions/INDEX.md` — all Architectural Decision Records; highlight reading:
  - **ADR-014** — L1 canonical storage paths (`~/.codeium/windsurf/`, NOT `~/.windsurf/`)
  - **ADR-015** — skill vs workflow invocation syntax (`@<skill>` vs `/<workflow>`)
  - **ADR-016** — workflow canonical path correction (`global_workflows/`)
  - **ADR-017** — `@propose-extension` as the single intake channel for system extensions
  - **ADR-018** — release-discipline cluster (`@release-manager` + `branch-and-pr-required` + 4 helper workflows)
- `docs/rules/INDEX.md` — long-form rule archive (reference only; concise active versions live in `~/.codeium/windsurf/memories/global_rules.md`)

## Where artifacts live

- **Active L1 surface** (unversioned, per deferred M-2 storage decision): `~/.codeium/windsurf/skills/<name>/SKILL.md`, `~/.codeium/windsurf/global_workflows/*.md`, `~/.codeium/windsurf/memories/global_rules.md`; plus `~/.windsurf/contracts/` and `~/.windsurf/templates/` (also unversioned)
- **Long-form documentation + ADRs + queue + retros**: this repo (`docs/` + `queue/` + `retros/`)
- **Vendored reference skills**: `refs/` (superpowers, mattpocock-skills, claude-skills, awesome-agent-skills — read-only, consulted per the `adapt-from-all` convention when authoring new L1 artifacts)

## Invocation quick-reference

- Skills: `@<skill-name>` (auto-activates on description-match; also supports `@skills:` dropdown for browsing)
- Workflows: `/<workflow-name>` (slash-only; `@` never resolves workflows)
- Rules: most are always-on or `model_decision` (silently active, no invocation syntax); only `manual`-trigger rules appear in the `@rules:` dropdown — we currently have zero

Full semantics + Windsurf docs citations in `docs/manual.md#invocation-cheat-sheet`.

## Adding or modifying anything in the system

Route through `@propose-extension` (ADR-017). It is the single intake channel for rule / skill / workflow / contract / L3 template / AGENTS.md / shared scaffold asset additions or modifications, across all layers (L1, L2, L3). It dispatches to `@write-skill` for skills, to `@update-horizontal` for L1 mutations of existing artifacts, or to direct-write for other routes.

**Never** author L1 artifacts under `~/.windsurf/skills/`, `~/.windsurf/workflows/`, or `~/.windsurf/rules/` — those paths are dead per ADR-014 and Windsurf does not scan them.

## Landing changes to `main` in any repo

Route through `@release-manager` (ADR-018). The skill owns the branch → commits → push → PR → CI → squash-merge → cleanup lifecycle, delegating to 4 helper workflows (`/branch-start`, `/branch-push-and-pr`, `/ci-watch`, `/branch-merge-and-cleanup`). **Never** `git push origin main` directly outside cold-start (exception defined in `docs/rules/branch-and-pr-required.md`).

## Sprint rhythm

Milestones close → `sprint-review-prompt` rule fires → invoke `@sprint-review` → drain `queue/pending-review.md` → approved L1 promotions hand off to `@update-horizontal` → retro closes with clean working tree (hard gate per ADR-008).

Plans live at `~/.windsurf/plans/<slug>-<suffix>.md` (outside this repo).
