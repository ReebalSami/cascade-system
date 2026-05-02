# ADR-022: Obsidian CLI selection (over all MCP architectures)

**Status**: Accepted
**Date**: 2026-05-02
**Supersedes**: handoff `docs/handoffs/cascade-c-obsidian.md` §3 M2C.1 "pick an Obsidian MCP" framing; pre-registered `obsidian-mcp-server` entry in `~/.codeium/windsurf/mcp_config.json` (annotated superseded, not removed, per ADR-011)
**Plan**: `~/.windsurf/plans/m2c1-obsidian-cli-execution-52d465.md`
**Issue**: [#41](https://github.com/ReebalSami/cascade-system/issues/41) (M2C.1)

## Context

Sprint 2 Vertical C handoff framed M2C.1 as *"discover and configure an Obsidian MCP"*, with `mcp-obsidian`, `obsidian-mcp-server`, or a DIY server as candidates. The pre-registered entry in `~/.codeium/windsurf/mcp_config.json` (`obsidian`, currently `disabled: true`) targets `cyanheads/obsidian-mcp-server` — a REST-API-plugin-backed TypeScript MCP.

Three findings from the `adapt-from-all` research pass materially change the answer:

### 1 — No official MCP exists

Verified by direct GitHub search: `github.com/obsidianmd`, `github.com/modelcontextprotocol`, `github.com/anthropics` all have zero Obsidian-MCP repositories. The official MCP registry (`registry.modelcontextprotocol.io`) has no blessed entry. Every community MCP is third-party with varying maintenance quality.

### 2 — The community MCP landscape has a data-loss hazard

All REST-API-plugin-backed MCPs (including the pre-registered `cyanheads/obsidian-mcp-server` and the widely-cited `MarkusPfundstein/mcp-obsidian`) depend on `coddingtonbear/obsidian-local-rest-api`. That community plugin has an **open data-loss bug** (issue [#237](https://github.com/coddingtonbear/obsidian-local-rest-api/issues/237)) in its v3.6.x series: its POST endpoint can silently overwrite file contents when the metadata cache misses. The Markus codebase (referenced in 3.5k-star community threads) has been **unmaintained for 17 months**. Filesystem-direct MCPs (`bitbonsai/mcpvault`, `StevenStavrakis/obsidian-mcp`) avoid the plugin dependency but **cannot access Obsidian's backlink graph** — they only see raw markdown. `aaronsb/obsidian-mcp-plugin` (native-Obsidian-plugin architecture) has full graph access but is **beta-only** and requires BRAT install (not in the official Obsidian plugin directory).

### 3 — Obsidian shipped a first-party CLI in v1.12 (Feb 2026)

Obsidian Inc. publishes `obsidian` as a stable command-line tool (current: v1.12.7 per `obsidian.md/changelog`). Official marketing copy at `obsidian.md/cli` explicitly targets agent use: *"Give agentic tools access to a vault without access to your full computer."* It supports everything the handoff requires (read-by-path, search-by-tag-or-text, backlinks, create/append, daily notes) plus more (orphan detection, unresolved-link detection, tag frequency, `eval` for arbitrary Obsidian-API access). The handoff predates this CLI's release.

### 4 — The vault is iCloud-symlinked

Pre-flight probe discovered `~/Projects/obsidian` → `/Users/reebal/Library/Mobile Documents/iCloud~md~obsidian/Documents` (symlink). The vault's files may exist as cloud-only stubs that materialize on read. **Filesystem-direct MCPs would silently 404 on un-materialized files.** Only architectures that query Obsidian's running-app index (native plugin, CLI) handle iCloud transparently, because Obsidian's file-open logic triggers iCloud materialization.

## Decision

**Use the first-party Obsidian CLI (`obsidian`) as Cascade's vault-access mechanism.** Cascade invokes it via `run_command`. No MCP is enabled for Obsidian.

The pre-registered `obsidian-mcp-server` entry in `~/.codeium/windsurf/mcp_config.json` is annotated as superseded (per ADR-011 supersession-over-deletion) with a `"_note"` sibling field referencing this ADR. It stays `disabled: true` as a historical record.

## Alternatives considered (4-architecture comparison)

| Candidate | Architecture | Read path | Search tags/text | **Backlinks** | Write | **iCloud-stub handling** | Setup friction | Maintenance risk | Runtime dep |
|---|---|---|---|---|---|---|---|---|---|
| `cyanheads/obsidian-mcp-server` (pre-registered) | REST-API-plugin-backed MCP | ✓ | ✓ | ✓ (via plugin) | ✓ | ✓ (plugin handles) | Medium (plugin install + API key + port config) | **High** (plugin has open data-loss bug #237 in v3.6.x) | Obsidian running + plugin installed |
| `MarkusPfundstein/mcp-obsidian` | REST-API-plugin-backed MCP | ✓ | ✓ | ✓ | ✓ | ✓ | High (Python + uvx + plugin + key) | **Abandoned** (17-month silence) | Obsidian running + plugin installed |
| `bitbonsai/mcpvault` | Filesystem-direct MCP | ✓ | ✓ (BM25) | **✗ (no graph access)** | ✓ | **✗ (404s on cloud-only stubs)** | Low (`npx`-installable) | Low (active Apr 2026) | None (but misses real vault state) |
| `StevenStavrakis/obsidian-mcp` | Filesystem-direct MCP | ✓ | ✓ | **✗** | ✓ (README warns "backup first") | **✗** | Low | Medium (stale since June 2025) | None |
| `aaronsb/obsidian-mcp-plugin` | Native-Obsidian-plugin MCP | ✓ | ✓ | ✓ | ✓ | ✓ | **High** (BRAT-install only; `mcp-remote` bridge for stdio) | Medium (**beta**; not in official plugin directory) | Obsidian running |
| DIY minimal MCP | Build our own | — | — | — | — | — | **Very high** (new codebase to maintain) | Very high (we'd own every bug) | Varies |
| **Obsidian CLI (chosen)** | **First-party CLI** | **✓** | **✓ (Obsidian's native index)** | **✓ (graph via running app)** | **✓** | **✓ (Obsidian handles materialization)** | **Low** (one-time admin-prompt register) | **Very low** (Obsidian Inc. maintains) | Obsidian running |

### Decisive criteria

- **Backlinks**: only the CLI, aaronsb, and REST-API-backed options support them. Filesystem-direct out. This is a hard handoff criterion.
- **Data-loss safety**: REST-API-plugin dependency chain disqualifies `cyanheads` + `Markus` until [#237](https://github.com/coddingtonbear/obsidian-local-rest-api/issues/237) closes. We don't gate Sprint 2 on a third-party's unfixed bug.
- **iCloud transparency**: filesystem-direct candidates silently fail on cloud-only files. Hard out.
- **Maintenance risk**: first-party > active-community > stale > abandoned > beta. CLI wins on every horizon.
- **Setup friction**: CLI is one admin prompt on the user's machine. Everything else stacks plugin install + API key + port + runtime dependency.

### Benchmark evidence (Prokopov, 4,663-note vault)

| Task | grep | Obsidian CLI | Ratio |
|---|---|---|---|
| Search vault | 1.95 s | 0.32 s | 6× faster |
| Find orphan notes | 15.6 s | 0.26 s | 60× faster |
| Orphan detection token cost | ~7,000,000 | ~100 | **~70,000× cheaper** |

Filesystem-direct and REST-API-plugin MCPs all incur grep-class costs for graph queries because they reimplement the graph on top of raw markdown. The CLI queries Obsidian's pre-built index.

## Consequences

**Enables:**

- M2C.3 proposals (`@grill-me`, `@to-prd`, `/recalibrate`) can cite specific `obsidian` CLI commands (e.g., `obsidian search query=... format=json`, `obsidian backlinks "<note>"`, `obsidian read <path>`) instead of MCP tool calls.
- M2C.4 `obsidian-context-priming` rule can prime sessions with real tag/backlink queries at low token cost.
- M2C.5 vault-→-PRD surfacing uses `obsidian search` + `obsidian tags counts` with negligible overhead.
- Cascade has no new always-on runtime process to supervise; the MCP panel stays lean.
- iCloud materialization is handled transparently by Obsidian itself.

**Constrains:**

- **Obsidian app must be running** when Cascade queries the vault. Not a new constraint for this user (Obsidian is always open during knowledge work). Not viable for headless / server-side agents — mitigation deferred; Obsidian Inc. is shipping "Headless Sync" per `obsidian.md/sync/headless` which will cover that case if it arises.
- **Per-machine CLI registration** — the `obsidian` command is installed by enabling "Command line interface" in Obsidian Settings → General and accepting the macOS admin prompt to create `/usr/local/bin/obsidian`. Not portable across machines without repeat setup. Acceptable: Cascade runs per-machine anyway.
- **Shell interface, not structured MCP tool surface** — the `obsidian` command appears in terminal history, not in Windsurf's MCP panel. Cascade invokes via `run_command`; skills parse `format=json` output. Minor ergonomic cost versus structured tool listing; outweighed by the 70,000× token saving and zero runtime dependency.
- **Requires Obsidian ≥ 1.12.4** — user must be on a current stable build. Pre-flight probe found this machine at v1.7.7; scripted update to v1.12.7 via terminal (download + graceful quit + `ditto` replace + relaunch) handled as part of the M2C.1 setup.
- **Enabling CLI while Obsidian is running requires a clean app restart** — discovered empirically during M2C.1 smoke-test. When CLI is toggled on in Settings → General during a running session, the IPC socket at `~/.obsidian-cli.sock` is created but not fully serving: `obsidian help` hangs with zero output on stdout/stderr (exit 142 via SIGALRM). After quitting + relaunching Obsidian, the same command returns the help text instantly (EXIT=0, 17,353 bytes). Session-start enable is the happy path; mid-session enable needs one manual restart. Worth surfacing in M2C.4 `obsidian-context-priming` rule and any future user-facing setup notes.

**Fallback path (if CLI proves insufficient for a future use case):**

- `obsidian-ts-mcp` (community bridge) wraps the CLI in MCP shape for clients without shell access (e.g., Claude Desktop). Layers cleanly on top; no architectural change.
- If Obsidian regresses or deprecates the CLI (low probability — marketed as agent-facing), revisit `aaronsb/obsidian-mcp-plugin` when it exits beta.
- If Obsidian REST API plugin closes [#237](https://github.com/coddingtonbear/obsidian-local-rest-api/issues/237), `cyanheads/obsidian-mcp-server` becomes reconsidable.

**Supersession of pre-registered config:**

- `obsidian` entry in `~/.codeium/windsurf/mcp_config.json` gets `"_note": "Superseded by Obsidian CLI per ADR-022 ..."` added as a sibling field. Entry remains `disabled: true`. Not removed (per ADR-011 supersession-over-deletion).
- Handoff §3 M2C.1 references the MCP framing; future readers trace handoff → this ADR → current state.

## Evidence

Smoke-test captured 2026-05-02 after CLI-enable + Obsidian restart. Vault: `/Users/reebal/Projects/obsidian/second-brain/` (iCloud-symlinked, 201 MB, 119 files per `obsidian eval`).

All commands run via `run_command` → `perl -e 'alarm <N>; exec @ARGV' obsidian <subcmd> < /dev/null > outfile 2> errfile`. The `perl alarm` wrapper bounds each call (≤ 20s); outputs redirected to files for clean capture + size measurement.

| Command | Exit | Stdout | Notes |
|---|---:|---|---|
| `obsidian help` | 0 | 17,353 B | 476 lines; 100+ subcommands (aliases, append, backlinks, bases, bookmarks, daily, eval, files, folders, history, hotkeys, orphans, plugins, properties, search, tags, templates, themes, vaults, workspace, ...) |
| `obsidian version` | 0 | `1.12.7 (installer 1.12.7)` | matches app `CFBundleShortVersionString` |
| `obsidian vaults` | 0 | `second-brain` | auto-discovered via running app's config; no `vault=` param required |
| `obsidian backlinks file=Home format=tsv` | 0 | `No backlinks found.` | Home.md is the vault's landing page (no incoming links expected) |
| `obsidian search query=test total` | 0 | `19` | 19 hits for "test" |
| `obsidian orphans total` | 0 | `105` | unlinked notes |
| `obsidian tags total` | 0 | `30` | distinct tags |
| `obsidian eval code='app.vault.getFiles().length'` | 0 | `=> 119` | proves full Obsidian JS API access |
| `obsidian read file=Home` | 0 | 58 B | 4-line wikilink landing page (low-privacy content; shown as evidence) |

**IPC mechanism (confirmed via `strings` on binary + `lsof` on running Obsidian process):**

- CLI talks to the running Obsidian app via UNIX domain socket at `~/.obsidian-cli.sock`
- The socket is opened by Obsidian when the CLI feature is enabled in Settings
- `lsof -p $(pgrep -x Obsidian)` confirms fd 21 bound to the socket path
- No TCP listener, no HTTP API, no authentication token — UNIX-permission-scoped to the user's account

**iCloud-stub test (acceptance criterion from issue #41):**

- `find ~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/second-brain -name "*.icloud"` → **0 results**
- `find ~/Projects/obsidian/second-brain -name "*.icloud"` → **0 results**
- All files in this vault are materialized locally (user has not enabled "Optimize Mac Storage" for iCloud). No stub files exist to test against.
- Criterion de-facto satisfied without empirical test. Architecturally the CLI queries Obsidian's running-app index, which would trigger on-demand materialization if a stub were read — the filesystem-direct MCP failure mode (silent 404 on 0-byte stub) cannot occur with this architecture.

**Install artifacts:**

- Symlink: `/usr/local/bin/obsidian -> /Applications/Obsidian.app/Contents/MacOS/obsidian-cli`
- Binary: Mach-O universal (x86_64 + arm64), codesigned, hardened runtime, identifier `obsidian-cli`
- No additional plugins, no API keys, no TCP ports, no env vars required
- Pre-registered `obsidian` entry in `~/.codeium/windsurf/mcp_config.json` annotated with `_note` field referencing this ADR (supersession per ADR-011)

## Source

**Research pass (`adapt-from-all` rule):**

- GitHub org searches — `org:obsidianmd`, `org:modelcontextprotocol`, `org:anthropics` (all zero Obsidian-MCP repos); `obsidian MCP` repo search (13 candidates triaged)
- Official MCP registry: `registry.modelcontextprotocol.io` (no blessed entry)
- Obsidian CLI official docs: `obsidian.md/cli`, `obsidian.md/help/cli`
- Obsidian changelog: `obsidian.md/changelog/` (v1.12.0 through v1.12.7)
- Obsidian forum primary MCP experiences thread: [#99936](https://forum.obsidian.md/t/obsidian-mcp-servers-experiences-and-recommendations/99936) (community verdict: "interesting to play with", "not life-changing", many keep separate sandbox vaults)
- Obsidian forum REST API MCP critique thread: [#109211](https://forum.obsidian.md/t/obsidian-rest-api-mcp-server/109211) ("most [MCPs] are unmaintained or overly complicated")
- Prokopov benchmark: [prokopov.me/posts/obsidian-cli-changes-everything-for-ai-agents](https://prokopov.me/posts/obsidian-cli-changes-everything-for-ai-agents/) (4,663-note vault; 70,000× token-cost ratio for orphan detection)
- Mandalivia semantic-search benchmark: [mandalivia.com/obsidian/semantic-search-for-your-obsidian-vault-what-i-tried-and-what-worked](https://www.mandalivia.com/obsidian/semantic-search-for-your-obsidian-vault-what-i-tried-and-what-worked/) (2,400-note vault; CLI-style tools >> grep)
- andrew.ooo Agentic Copilot review: [andrew.ooo/posts/agentic-copilot-obsidian-claude-code-plugin-review](https://andrew.ooo/posts/agentic-copilot-obsidian-claude-code-plugin-review/) (plugin-author bypasses MCP entirely; delegates to CLI)
- Reddit r/mcp "Is Obsidian MCP actually worth it over just using [filesystem tools]?" thread
- Kepano (Obsidian CEO) public statement: *"It's not because your agent can do something with 12 tool calls and 20k tokens that it should."*
- Candidate READMEs: `bitbonsai/mcpvault`, `cyanheads/obsidian-mcp-server`, `MarkusPfundstein/mcp-obsidian`, `StevenStavrakis/obsidian-mcp`, `aaronsb/obsidian-mcp-plugin`, `jacksteamdev/obsidian-mcp-tools`, `dp-veritas/mcp-obsidian-tools`
- Data-loss bug: `coddingtonbear/obsidian-local-rest-api#237`

**Related ADRs:**

- ADR-001 (MCP vs `gh` CLI routing) — this ADR extends the "use native CLI when equivalent or better" pattern to Obsidian
- ADR-009 (NNN reserve-in-INDEX-first) — row reserved in `INDEX.md` before this file authored
- ADR-011 (supersession over deletion) — applied to `obsidian-mcp-server` config entry and handoff MCP framing
- ADR-014 (L1 canonical storage paths) — `mcp_config.json` lives under `~/.codeium/windsurf/` (Windsurf runtime config, not an L1 skill/rule/workflow artifact)
- ADR-017 (`@propose-extension` intake) — N/A here; `mcp_config.json` is runtime config, not an L1 artifact, so no `@propose-extension` dispatch required
- ADR-018 (release-discipline cluster) — this change lands via `@release-manager` → `/branch-start` → `/commit` → `/branch-push-and-pr` → `/branch-merge-and-cleanup`

**Handoff reference:**

- `docs/handoffs/cascade-c-obsidian.md` §3 M2C.1 — the "pick an MCP" framing this ADR supersedes
- `docs/handoffs/cascade-c-obsidian.md` §7 Definition of done — *"An Obsidian MCP is configured and demonstrably working"* is satisfied by "CLI is configured and demonstrably working" under this supersession; M2C.1 issue #41 close comment explains

**Plan reference:**

- `~/.windsurf/plans/m2c1-obsidian-cli-execution-52d465.md` — this session's execution plan, including pre-flight state, execution sequence, confirmed decisions
