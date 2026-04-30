# cascade-system

Meta-repo for the Cascade Project Lifecycle System — the layered, time-free, count-free Cascade configuration that bootstraps and maintains professional projects across stacks.

## What lives here

| Path | Purpose |
|---|---|
| `refs/` | Vendored reference repos (read-only): `obra/superpowers`, `mattpocock/skills`, `alirezarezvani/claude-skills`, `VoltAgent/awesome-agent-skills`. Studied for adaptation; never runtime-depended on. **Gitignored** — clone locally per machine. |
| `retros/` | Cross-project sprint retrospectives. Each vertical Cascade contributes here at sprint exits. |
| `queue/` | `pending-review.md` — drained by `@sprint-review` skill. Captures `#capture`-tagged thoughts from any active Cascade. |
| `docs/` | Architecture, decisions, technical notes for the system itself. We dogfood `@docs-refresh` here. |

## Where the actual L1 components live

Not here. The L1 system lives at `~/.codeium/windsurf/` (skills + global_workflows) and `~/.codeium/windsurf/memories/global_rules.md` (rules). This repo tracks the **meta-work**: the building of L1, sprint coordination, retro archive, and self-tracking. See `docs/manual.md` "Where things live" for the canonical path table.

## Tracking

This repo has its own GitHub Project (linked to `ReebalSami/cascade-system`) where every milestone of every sprint gets an issue. The PM agent (`@sync-github`) owns this Project once Sprint 1 milestone M1.8 lands.

## Plan

The living plan: `~/.windsurf/plans/cascade-project-system-cac5f9.md`.

## Principles

See `~/Projects/cascade-system/docs/rules/`:
- `no-time-estimates`
- `no-quantity-over-shape`
- `adapt-from-all`
- `document-as-you-go`
- `plan-drift-watcher`
- … plus rules extracted from `portfolio-website`.

## Contributing

This is a personal system. Forks welcome for inspiration — but the architecture is opinionated to one developer's flow.

## License

MIT.
