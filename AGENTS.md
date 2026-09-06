# AGENTS.md — cascade-system meta-repo

This is **cascade-system** — the L1 meta-repository for the Cascade personal system. Artifacts authored here govern every project under `~/Projects/`.

## Start here

- `docs/cheat-sheet.md` — **scannable single-page inventory** (every skill, workflow, rule, contract, template + how to start work + how to extend the system). Read this first if you want the *what*.
- `docs/manual.md` — narrative system manual (mental model, invocation cheat-sheet, release discipline, full "where things live" reference). Read this for the *why*.
- `docs/architecture/system-overview.mmd` — single Mermaid diagram of the layered architecture, entry-point skills, and primary flows. Renders in GitHub markdown previews.
- `docs/decisions/INDEX.md` — all Architectural Decision Records; highlight reading:
  - **ADR-014** — L1 canonical storage paths (`~/.codeium/windsurf/`, NOT `~/.windsurf/`)
  - **ADR-015** — skill vs workflow invocation syntax (`@<skill>` vs `/<workflow>`) — invocation table superseded by ADR-037
  - **ADR-016** — workflow canonical path correction (`global_workflows/`) — stubs only since ADR-037
  - **ADR-017** — `@propose-extension` as the single intake channel for system extensions
  - **ADR-018** — release-discipline cluster (`@release-manager` + `branch-and-pr-required` + 4 manual helper skills)
  - **ADR-019** — `@begin` as front-door entry skill for new-idea project starts
  - **ADR-020** — `@kickoff` as front-door entry skill for vertical Cascade pickups
  - **ADR-037** — Devin runtime adoption cluster: workflows re-homed as manual skills (`triggers: [user]`), consumer paths `.devin/` + `.agents/skills/`, `/start-project` copies only on-demand rules, shared `AGENTS.md` + `CLAUDE.md`, strict-YAML skill frontmatter
- `docs/skills/INDEX.md` and `docs/workflows/INDEX.md` — pointer indexes to canonical L1 paths (auto-maintained by `@docs-refresh`)
- `docs/rules/INDEX.md` — long-form rule archive (reference only; concise active versions live in `~/.codeium/windsurf/memories/global_rules.md`)

## Where artifacts live

- **Active L1 surface** (unversioned, per deferred M-2 storage decision): `~/.codeium/windsurf/skills/<name>/SKILL.md` (read by Devin CLI and Windsurf — the single content home, former workflows included), `~/.codeium/windsurf/memories/global_rules.md`; `~/.codeium/windsurf/global_workflows/*.md` are redirect stubs only (ADR-037); plus `~/.windsurf/contracts/` and `~/.windsurf/templates/` (also unversioned)
- **Consumer projects** (bootstrapped by `/start-project`): `.devin/phases.yaml`, `.devin/rules/` (six on-demand L1 rules + `strict-docs-placement` + L3 rules), `.agents/skills/` (L3 + third-party skills), `AGENTS.md` + `CLAUDE.md`. Legacy `.windsurf/` is read as a fallback only.
- **Long-form documentation + ADRs + queue + retros**: this repo (`docs/` + `queue/` + `retros/`)
- **Vendored reference skills**: `refs/` (superpowers, mattpocock-skills, claude-skills, awesome-agent-skills — read-only, consulted per the `adapt-from-all` convention when authoring new L1 artifacts)

## Invocation quick-reference

- Every procedure is a skill. Devin CLI: `/<name>`. Windsurf: `@<name>` (or `/<name>` via the redirect stub for the ten former workflows).
- In these docs `@<name>` marks a judgment-heavy skill that auto-activates on description match; `/<name>` marks a manual deterministic one (`triggers: [user]` + `activation: manual`; never auto-invoked). Same artifact kind, different activation (ADR-037).
- Rules: most are always-on or `model_decision` (silently active, no invocation syntax); every rule file declares `trigger:` explicitly; only `manual`-trigger rules appear in Windsurf's `@rules:` dropdown — we currently have zero

Full semantics + Devin/Windsurf docs citations in `docs/manual.md#invocation-cheat-sheet`.

## How to start work

- **New idea / new project** → invoke `@begin` and pass the idea or note path. Auto-activates on phrasing like *"I have a new idea"*, *"start a new project"*, *"@begin <idea-note>"*. Orchestrates `@grill-me` → stack-decision → `/add-project-type` (if needed) → `/start-project` → `/run-phase brainstorm`. Per ADR-019.
- **Pick up a vertical** (Sprint 2 verticals B/C/D from a handoff doc) → invoke `@kickoff` and pass the handoff path. Auto-activates on *"pick up Cascade C"*, *"@kickoff <handoff-path>"*. Reads handoff + parent plan + cited ADRs + cheat-sheet, detects lifecycle position, files missing milestones, asks ONE focused starting question. Per ADR-020.
- **Resume an existing project** → just open the project directory; its `AGENTS.md` auto-loads, L2 rules auto-load, and `/run-phase` (no arg) lists current phases with artifact status.

`@begin` and `@kickoff` together obsolete hand-authored kickoff plans at `~/.windsurf/plans/<vertical>-kickoff-*.md` (legacy plans preserved per ADR-011 supersession-over-deletion; new flows route through the skills).

## Adding or modifying anything in the system

Route through `@propose-extension` (ADR-017). It is the single intake channel for rule / skill / workflow / contract / L3 template / AGENTS.md / shared scaffold asset additions or modifications, across all layers (L1, L2, L3). It dispatches to `@write-skill` for skills, to `@update-horizontal` for L1 mutations of existing artifacts, or to direct-write for other routes.

**Never** author L1 artifacts under `~/.windsurf/skills/`, `~/.windsurf/workflows/`, or `~/.windsurf/rules/` — those paths are dead per ADR-014 and neither tool scans them. **Never** add content under `~/.codeium/windsurf/global_workflows/` — Devin does not import workflows; a new deterministic procedure is a manual skill (ADR-037). Skill frontmatter must parse as strict YAML or Devin silently drops the skill — run `@verify-l1` before any L1-touching PR.

## Landing changes to `main` in any repo

Route through `@release-manager` (ADR-018). The skill owns the branch → commits → push → PR → CI → squash-merge → cleanup lifecycle, delegating to 4 manual helper skills (`/branch-start`, `/branch-push-and-pr`, `/ci-watch`, `/branch-merge-and-cleanup`). **Never** `git push origin main` directly outside cold-start (exception defined in `docs/rules/branch-and-pr-required.md`).

## Sprint rhythm

Milestones close → `sprint-review-prompt` rule fires → invoke `@sprint-review` → drain `queue/pending-review.md` → approved L1 promotions hand off to `@update-horizontal` → retro closes with clean working tree (hard gate per ADR-008).

Plans live at `~/.windsurf/plans/<slug>-<suffix>.md` (outside this repo).
