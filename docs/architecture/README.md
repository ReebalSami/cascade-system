# Architecture — cascade-system meta-repo

Hosts system-overview and cross-Cascade flow diagrams. Also hosts cross-cutting design brainstorms (e.g., `parked-items-brainstorm.md`) when no `docs/prompts/stages/` exists in this meta-repo per `docs/structure.md` §6. The first diagram landed in Sprint 1.5.5; the remaining two stay deferred until the multi-Cascade flow has stabilized across Sprint 2 verticals.

## Intended contents

| Diagram | Purpose | Format | Status |
|---|---|---|---|
| `system-overview.mmd` | L0 → L1 → L2 → L3 layers + 5 entry-point skills + 4 primary flows + extension side-channel | Mermaid | **active** (Sprint 1.5.5, this PR) |
| `cross-cascade-flow.mmd` | Sprint-to-vertical-spawn handoff flow + `/sprint-review` ↔ `/update-horizontal` ↔ queue cycle | Mermaid | pending |
| `phase-taxonomy.excalidraw` | Conceptual sketch of the `phases.yaml` contract and its readers | Excalidraw | pending |

## Why this README exists (not just a `.gitkeep`)

Per ADR-010, empty subdirectories without rationale are warnings in `/docs-refresh`. This README makes the empty-but-reserved state explicit, discharges the warning, and gives future diagrammers a concrete backlog to pick from.

## Source

- ADR-010 (Sprint 1 hardening) — codified the empty-dir rationale requirement
- ADR-007 (sprint-1 review subdirectory) — tangentially mentioned `architecture/` as a natural home for cross-Cascade flow diagrams
- Shared scaffold `~/.windsurf/templates/_shared/scaffold/docs/architecture/README.md` — the downstream-project variant of this README; kept intentionally different (downstream projects get example diagrams; the meta-repo does not)
