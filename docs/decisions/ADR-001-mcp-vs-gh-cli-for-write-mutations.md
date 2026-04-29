# ADR-001: MCP vs `gh` CLI for GitHub write mutations

**Status**: Accepted
**Date**: Sprint 0, M0.1

## Context

During M0.1 (meta-repo creation), `mcp3_create_repository` returned `403 Resource not accessible by personal access token`. The `GITHUB_TOKEN` env PAT used by the GitHub MCP server (`api.githubcopilot.com/mcp/`) lacks `repo` write scope intentionally — it's scoped narrowly for security.

The locally-installed `gh` CLI carries two tokens:

1. The same `GITHUB_TOKEN` env PAT (active by default, narrow scope)
2. A keyring token `gho_...` (broader scope, set up via `gh auth login`)

Bypassing the env PAT via `GITHUB_TOKEN= GH_TOKEN= gh ...` makes `gh` use the keyring token, which has full repo + project write scope.

In addition, the GitHub MCP doesn't expose all GitHub Projects v2 mutation endpoints (e.g., `gh project create`, `gh project item-add`). Even if the MCP token had broader scope, some operations require `gh`.

## Decision

**Hybrid routing** for GitHub state changes:

| Operation | Tool |
|---|---|
| Read repo, list issues/PRs/branches/commits | GitHub MCP (`mcp3_*`) |
| Create / update / close issues | GitHub MCP (`mcp3_issue_write`) |
| Create / update PRs, comments, reviews | GitHub MCP |
| **Create repository** | `gh` CLI with token bypass |
| **Create / configure GitHub Project v2** | `gh` CLI |
| **Add issue to Project, move card** | `gh` CLI |
| **Create branch / push** | `git` CLI (already authed via SSH) |

The `gh` invocation pattern across all skills:

```sh
GITHUB_TOKEN= GH_TOKEN= gh <command> ...
```

This always selects the keyring token, regardless of env state.

## Alternatives considered

- **Add `repo` scope to env PAT** — Rejected. The env PAT's narrow scope is intentional; broadening it weakens the principle of least privilege.
- **Use `gh` for everything** — Rejected. The MCP is faster and integrates cleanly with Cascade's tool-calling model for reads + simple issue operations.
- **Use MCP for everything (request scope from upstream)** — Rejected. The MCP server is operated by GitHub Copilot infra; we don't control its scopes.
- **Switch to a self-hosted GitHub MCP** (e.g., `github/github-mcp-server` running locally with our own PAT) — Deferred. Adds operational complexity. Revisit if hybrid routing becomes painful.

## Consequences

- Every L1 skill that mutates GitHub state must know which tool to call. The `/sync-github` skill (M1.8) encapsulates this routing.
- Documented as the canonical pattern in `~/Projects/cascade-system/docs/handoffs/cascade-a-sprint-1.md`.
- Sprint 1 issues #5 (`/start-project`) and #9 (`/sync-github`) inherit this constraint.
- Future Cascade deployments on machines without `gh` keyring auth need an alternative — captured as a future task: "ensure `gh auth login` ran during L0 system setup."

## Source

- Sprint 0 retro: `~/Projects/cascade-system/retros/sprint-0.md` (friction note #1)
- M0.1 commit: `9579587`
- Failing call: `mcp3_create_repository` 403 response observed during Sprint 0 M0.1 execution
